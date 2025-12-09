| Entitas         | Kegunaan Utama                                 |
| --------------- | ---------------------------------------------- |
| **Users**       | Menyimpan data pengguna aplikasi.              |
| **Products**    | Menyimpan daftar produk & harga.               |
| **Carts**       | Mewakili keranjang belanja milik user.         |
| **Cart_Items**  | Menyimpan isi keranjang dan quantities.        |
| **Orders**      | Mewakili transaksi saat user checkout.         |
| **Order_Items** | Menyimpan produk yang dibeli dalam satu order. |

1. Relasi: User — Cart (One to One)
Deskripsi
Setiap User hanya memiliki satu keranjang (Cart).
Dan setiap Cart hanya dimiliki oleh satu User.
Alasan
Keranjang biasanya melekat pada user yang sedang login.
Tidak masuk akal jika satu user punya banyak keranjang aktif.

Implementasi
Pada tabel carts terdapat user_id UNIQUE → menandakan hanya satu cart per user.

2. Relasi: Cart — CartItem (One to Many)
Deskripsi
Satu Cart dapat berisi banyak CartItem.
Tapi satu CartItem hanya boleh milik satu Cart.
Alasan
Keranjang belanja berisi banyak produk.
Setiap baris cart_items adalah 1 produk + quantity.
Contoh
Keranjang ID 10 bisa berisi:
Item Produk 3,
Item Produk 7,
Item Produk 15, dst.

3. Relasi: Product — CartItem (One to Many)
Deskripsi
Satu Product dapat muncul di banyak CartItem, milik user berbeda.
Tapi satu CartItem hanya untuk satu Product.
Alasan
Banyak user bisa memasukkan produk yang sama.
Tapi satu cart_item tidak mungkin mencatat lebih dari satu produk.
Tambahan
Pada tabel cart_items, terdapat product_id sebagai foreign key.
Unique Constraint
UNIQUE (cart_id, product_id)
Mencegah user memasukkan produk yang sama dua kali ke keranjang.
Jika user tambah produk sama → hanya menaikkan quantity, bukan membuat baris baru.

5. Relasi: User — Order (One to Many)
Deskripsi
Satu User bisa membuat banyak Order (riwayat belanja).
Tapi satu Order hanya dimiliki oleh satu User.
Alasan
User bisa checkout berkali-kali.
Implementasi
Tabel orders memiliki user_id sebagai foreign key.

5. Relasi: Order — OrderItem (One to Many)
Deskripsi
Satu Order bisa memiliki banyak OrderItem.
Tapi satu OrderItem hanya untuk satu Order.
Alasan
Dalam satu pesanan, user membeli banyak produk.
Setiap product di order dicatat sebagai satu OrderItem.

6. Relasi: Product — OrderItem (One to Many)
Deskripsi
Satu Product bisa muncul di banyak OrderItem** (di banyak pesanan).
Tapi satu OrderItem hanya merepresentasikan 1 product.
Catatan penting
Harga di OrderItem disalin dari harga produk saat checkout, sehingga jika harga produk berubah di masa depan, order historis tetap benar.



SQL
CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100) UNIQUE
);


CREATE TABLE products (
    product_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(150),
    price DECIMAL(10,2),
    stock INT
);


CREATE TABLE carts (
    cart_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT UNIQUE,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);


CREATE TABLE cart_items (
    cart_item_id INT AUTO_INCREMENT PRIMARY KEY,
    cart_id INT,
    product_id INT,
    quantity INT DEFAULT 1,
    UNIQUE (cart_id, product_id),
    FOREIGN KEY (cart_id) REFERENCES carts(cart_id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);


CREATE TABLE orders (
    order_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    total_price DECIMAL(10,2),
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);


CREATE TABLE order_items (
    order_item_id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT,
    product_id INT,
    price DECIMAL(10,2),
    quantity INT,
    FOREIGN KEY (order_id) REFERENCES orders(order_id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);

