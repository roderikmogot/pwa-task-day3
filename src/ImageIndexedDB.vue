<template>
    <div>
        <input type="file" @change="handleFileChange" accept="image/*" />
        <img :src="imageUrl" v-if="imageUrl" alt="Uploaded Image" />
        <button @click="deleteImage">Delete</button>
    </div>
</template>
  
<script>
export default {
    data() {
        return {
            imageUrl: null,
        }
    },
    mounted() {
        // this.readImage();   // load image dari indexedDb
    },
    methods: {
        handleFileChange(event) {
            const file = event.target.files[0];
            const reader = new FileReader();

            reader.onload = (e) => {
                this.imageUrl = URL.createObjectURL(file);
                this.saveImageToIndexedDB(e.target.result);
            };

            reader.readAsArrayBuffer(file);
        },
        saveImageToIndexedDB(blob) {
            const request = indexedDB.open("roderik_yohanes_mogot", 2);

            request.onupgradeneeded = (event) => {
                const db = event.target.result;
                const objectStore = db.createObjectStore("images", { keyPath: "id", autoIncrement: true });
            };

            request.onsuccess = (event) => {
                const db = event.target.result;

                const transaction = db.transaction("images", "readwrite");
                const objectStore = transaction.objectStore("images");
                const addRequest = objectStore.add(blob);

                addRequest.onsuccess = () => {
                    this.readImage();
                    console.log("Berhasil menambahkan ke IndexedDB");
                };

                addRequest.onerror = (event) => {
                    console.error("Error adding data", event.target.error);
                };

                transaction.oncomplete = () => {
                    db.close();
                };
            };
        },
        readImage() {
            const request = indexedDB.open("roderik_yohanes_mogot", 2);

            request.onerror = (event) => {
                console.error("Error opening database:", event.target.error);
            };

            request.onsuccess = (event) => {
                try {
                    const db = event.target.result;
                    const readTransaction = db.transaction("images", "readonly");
                    const customerObjectStore = readTransaction.objectStore("images");
                    const customersCursor = customerObjectStore.openCursor();

                    customersCursor.onsuccess = (event) => {
                        const cursor = event.target.result;

                        if (cursor) {
                            const imageBuffer = cursor.value;
                            const imageBlob = new Blob([imageBuffer], { type: 'image/jpeg' });
                            const image = URL.createObjectURL(imageBlob);

                            this.imageUrl = image;

                            cursor.continue();
                        } else {
                            console.log("Data read successfully");
                            db.close();
                        }
                    };

                    customersCursor.onerror = (event) => {
                        console.error("Error reading data", event.target.error);
                        db.close();
                    };
                } catch (err) {
                    console.log("masuk")
                    return;
                }
            };
        },
        deleteImage() {
            const request = indexedDB.open("roderik_yohanes_mogot", 2);

            request.onerror = (event) => {
                console.error("Error opening database:", event.target.error);
            };

            request.onsuccess = (event) => {
                const db = event.target.result;

                const deleteTransaction = db.transaction("images", "readwrite");
                const deleteObjectStore = deleteTransaction.objectStore("images");

                const deleteRequest = deleteObjectStore.clear();

                deleteRequest.onsuccess = (event) => {
                    console.log("Data deleted successfully");
                    this.imageUrl = null;
                };

                deleteRequest.onerror = (event) => {
                    console.error("Error deleting data", event.target.error);
                };

                deleteTransaction.oncomplete = () => {
                    console.log("Delete transaction completed");
                    db.close();
                };
            };
        },
    },
};
</script>
  