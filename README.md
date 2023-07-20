# ts-mern-amazona

# UJIAN AKHIR SEMESTER PBO, PRAKTIKUM PBO, PRAKTIKUM BASIS DATA

### 1. Mampu menunjukkan keseluruhan Use Case beserta ranking dari tiap Use Case dari produk digital:
- Use case user

| Actor | Case | Priority |
| --- | --- | --- |
| Pembeli | melihat produk | 10 |
| Pembeli | melakukan check out | 10 |
| Pembeli | menambahkan produk ke keranjang | 10 |
| Pembeli | melakukan pembayaran | 10 |
| Pembeli | memilih metode pembayaran | 8 |
| Pembeli | menerima pesanan | 10 |
| Pembeli | mengkonfirmasi pesanan telah diterima | 10 |
| Pembeli | mereview produk pesanan | 8 |
| Penjual | posting produk | 10 |
| Penjual | memverifikasi pesanan | 8 |
| Penjual | mengirim pesanan ke ekspedisi | 10 |
| Penjual | mendapat konfirmasi penerimaan barang | 10 |
| Penjual | menerima pembayaran pesanan | 10 |
| Expedisi | menerima pesanan dari penjual | 10 |
| Ekpedisi | mengantar pesanan ke pembeli | 10 |
| Expedisi | menerima pembayaran ongkos kirim | 10 |
| Tamu | Daftar | 6 |
| Tamu | Login | 6 |
| Tamu | Melihat produk | 6 |
- Use case manajemen perusahaan

| Actor | Case | Priority |
| --- | --- | --- |
| Admin/System | memproses pesanan | 10 |

- Use case direksi perusahaan (dashboard, monitoring, analisis)

| Actor | Case | Priority |
| --- | --- | --- |
| Admin/System | memproses pesanan | 10 |
| Direktur | Mendapatkan sebaran informasi nama pengguna berdasarkan usia | 10 |
| Direktur | Mendapatkan informasi sebaran pengguna berdasarkan wilayah | 10 |
| Direktur | Mendapatkan informasi toko dengan penjualan terbanyak setiap bulan & terurut dari tertinggi | 10 |
| Direktur | Mendapatkan informasi produk terlaris | 10 |
| Direktur | Mendapatkan informasi expedition yang paling sering dipakai pengguna | 10 |

### 2. Mampu mendemonstrasikan Class Diagram dari keseluruhan Use Case produk digital

![class-diagram](https://gitlab.com/ansyrllah/informatics-student/-/raw/main/tokopedia-lite/class-diagram.gif)

### 3. Mampu menunjukkan dan menjelaskan penerapan setiap poin dari SOLID Design Principle
- Single Responsibility Principle (SRP)
```
  class ShippingAddress {
    @prop()
    public fullName?: string
    @prop()
    public address?: string
    @prop()
    public city?: string
    @prop()
    public postalCode?: string
    @prop()
    public country?: string
    @prop()
    public lat?: number
    @prop()
    public lng?: number
  }
  class Item {
    @prop({ required: true })
    public name!: string
    @prop({ required: true })
    public quantity!: string
    @prop({ required: true })
    public image!: number
    @prop({ required: true })
    public price!: number
    @prop({ ref: Product })
    public product?: Ref<Product>
  }
  export class Order {
    public _id!: string
    @prop()
    public orderItems!: Item[]
    @prop()
    public shippingAddress?: ShippingAddress
  
    @prop({ ref: User })
    public user?: Ref<User>
  
    @prop({ required: true })
    public paymentMethod!: string
  
    @prop()
    public paymentResult?: PaymentResult
  
    @prop({ required: true, default: 0 })
    public itemsPrice!: number
    @prop({ required: true, default: 0 })
    public shippingPrice!: number
    @prop({ required: true, default: 0 })
    public taxPrice!: number
    @prop({ required: true, default: 0 })
    public totalPrice!: number
    @prop({ required: true, default: false })
    public isPaid!: boolean
    @prop()
    public paidAt!: Date
    @prop({ required: true, default: false })
    public isDelivered!: boolean
    @prop()
    public deliveredAt!: Date
  }
```
Setiap kelas memiliki tanggung jawab yang terbatas pada bidangnya masing-masing. Misalnya, kelas Order bertanggung jawab untuk menyimpan detail pesanan, termasuk item pesanan, alamat pengiriman, metode pembayaran, dan informasi lainnya terkait pesanan. Ini memisahkan tanggung jawab antara entitas pesanan dengan entitas lainnya seperti produk (Product) dan pengguna (User).

```
  userRouter.post(
    '/signin',
    asyncHandler(async (req: Request, res: Response) => {
      const user = await UserModel.findOne({ email: req.body.email })
      if (user) {
        if (bcrypt.compareSync(req.body.password, user.password)) {
          res.json({
            _id: user._id,
            name: user.name,
            email: user.email,
            isAdmin: user.isAdmin,
            token: generateToken(user),
          })
          return
        }
      }
      res.status(401).json({ message: 'Invalid email or password' })
    })
  )

  userRouter.post(
    '/signup',
    asyncHandler(async (req: Request, res: Response) => {
      const user = await UserModel.create({
        name: req.body.name,
        email: req.body.email,
        password: bcrypt.hashSync(req.body.password),
      } as User)
      res.json({
        _id: user._id,
        name: user.name,
        email: user.email,
        isAdmin: user.isAdmin,
        token: generateToken(user),
      })
    })
  )

  userRouter.put(
    '/profile',
    isAuth,
    asyncHandler(async (req: Request, res: Response) => {
    const user = await UserModel.findById(req.user._id)
    if (user) {
      user.name = req.body.name || user.name
      user.email = req.body.email || user.email
      if (req.body.password) {
        user.password = bcrypt.hashSync(req.body.password, 8)
      }
      const updatedUser = await user.save()
      res.send({
        _id: updatedUser._id,
        name: updatedUser.name,
        email: updatedUser.email,
        isAdmin: updatedUser.isAdmin,
        token: generateToken(updatedUser),
      })
      return
    }
```
Setiap handler fungsi di dalam router bertanggung jawab untuk tugas spesifik. Misalnya, fungsi handler signin bertanggung jawab untuk memvalidasi masuknya pengguna, fungsi handler signup bertanggung jawab untuk membuat pengguna baru, dan fungsi handler profile bertanggung jawab untuk memperbarui profil pengguna. Setiap fungsi handler ini memiliki tanggung jawab yang terbatas pada tugasnya masing-masing.

- Open-Closed Principle
```
userRouter.post(
  '/signin',
  asyncHandler(async (req: Request, res: Response) => {
    const user = await UserModel.findOne({ email: req.body.email })
    if (user) {
      if (bcrypt.compareSync(req.body.password, user.password)) {
        res.json({
          _id: user._id,
          name: user.name,
          email: user.email,
          isAdmin: user.isAdmin,
          token: generateToken(user),
        })
        return
      }
    }
    res.status(401).json({ message: 'Invalid email or password' })
  })
)

userRouter.post(
  '/signup',
  asyncHandler(async (req: Request, res: Response) => {
    const user = await UserModel.create({
      name: req.body.name,
      email: req.body.email,
      password: bcrypt.hashSync(req.body.password),
    } as User)
    res.json({
      _id: user._id,
      name: user.name,
      email: user.email,
      isAdmin: user.isAdmin,
      token: generateToken(user),
    })
  })
)

userRouter.put(
  '/profile',
  isAuth,
  asyncHandler(async (req: Request, res: Response) => {
    const user = await UserModel.findById(req.user._id)
    if (user) {
    user.name = req.body.name || user.name
    user.email = req.body.email || user.email
    if (req.body.password) {
      user.password = bcrypt.hashSync(req.body.password, 8)
    }
    const updatedUser = await user.save()
    res.send({
      _id: updatedUser._id,
      name: updatedUser.name,
      email: updatedUser.email,
      isAdmin: updatedUser.isAdmin,
      token: generateToken(updatedUser),
    })
    return
  }
)
```
Dalam kode ini, OCP diterapkan dengan baik. Fungsi signin, signup, dan profile terbuka untuk perluasan melalui ekstensi atau penambahan logika tambahan, tetapi tertutup untuk modifikasi langsung. Anda dapat menambahkan fitur tambahan atau logika baru tanpa mengubah fungsi yang sudah ada.

- Liskov Substitution Principle
- Interface Segregation Principle
- Dependency Inversion Principle
```
  // dalam kelas Item
  @prop({ ref: Product })
  public product?: Ref<Product>

  // dalam kelas Order
  @prop({ ref: User })
  public user?: Ref<User>
```
Dalam kedua properti diatas, saya menggunakan ref untuk mengacu pada kelas Product dan User sebagai dependensi. Dengan menggunakan referensi (Ref) sebagai jenis tipe properti, saya membuat kode lebih fleksibel dalam menggantikan implementasi konkret dengan abstraksi. 
Misalnya, dalam kasus ini, Item tidak bergantung langsung pada implementasi konkret Product atau User, tetapi hanya bergantung pada abstraksi mereka melalui referensi. Ini memungkinkan untuk dengan mudah mengganti implementasi konkret Product atau User di masa depan tanpa mempengaruhi kelas Item itu sendiri.
Dengan menggunakan injeksi dependensi seperti ini, Anda menerapkan prinsip DIP dengan memastikan bahwa kelas Item bergantung pada abstraksi (Ref) daripada implementasi konkret (Product dan User). Ini memungkinkan fleksibilitas, pengujian yang lebih mudah, dan pemisahan tanggung jawab antara kelas Item dan implementasi konkret Product dan User.

### 4. Mampu menunjukkan dan menjelaskan Design Pattern yang dipilih
Penerapan pola desain Model-View-Controller (MVC) membagi komponen perangkat lunak menjadi tiga bagian terpisah yaitu Model, View, dan Controller. Setiap bagian memiliki tanggung jawab yang terpisah dan berfungsi dalam cara yang terdefinisi dengan jelas.

1. Model:
   - Model mewakili data dan logika bisnis dari aplikasi. Ini adalah bagian yang bertanggung jawab untuk mengelola dan memanipulasi data yang digunakan dalam aplikasi. Model sering terhubung dengan sumber data seperti basis data atau API untuk melakukan operasi CRUD (Create, Read, Update, Delete) pada data. Dalam beberapa kasus, Model juga dapat berisi validasi data dan bisnis logika tambahan.
   
   Potongan kode di bawah ini merupakan contoh implementasi Model dalam pola MVC pada `userModel.ts`:
   
   ```typescript
   import { modelOptions, prop, getModelForClass } from '@typegoose/typegoose'
   
   @modelOptions({ schemaOptions: { timestamps: true } })
   export class User {
     public _id?: string
   
     @prop({ required: true })
     public name!: string
   
     @prop({ required: true })
     public email!: string
   
     @prop({ required: true })
     public password!: string
   
     @prop({ default: false })
     public isAdmin!: boolean
   }
   
   export const UserModel = getModelForClass(User)
   ```

2. View:
   - View bertanggung jawab untuk tampilan dan representasi visual dari data yang diberikan oleh Model. View tidak memiliki logika bisnis atau akses langsung ke data, tetapi hanya menampilkan data yang diberikan oleh Controller dan berinteraksi dengan pengguna. View dapat berupa tampilan HTML, antarmuka pengguna grafis (GUI), atau bahkan tampilan teks sederhana.

   Dalam konteks aplikasi web, tampilan sering kali diimplementasikan menggunakan templating engine atau framework front-end seperti React, Angular, atau Vue. Contoh kode View dalam pola MVC dapat berupa kode HTML atau template.

3. Controller:
   - Controller bertindak sebagai perantara antara Model dan View. Itu menerima input dari pengguna melalui View, memproses input tersebut, dan berkomunikasi dengan Model untuk mengambil atau memperbarui data yang diperlukan. Setelah memperoleh data yang relevan, Controller akan memutuskan tampilan mana yang harus ditampilkan dan mengirimkannya kembali ke View untuk ditampilkan kepada pengguna.

   Dalam contoh kode `userRouter.ts`, fungsi-fungsi seperti `signin`, `signup`, dan `profile` bertindak sebagai Controller dalam pola MVC. Mereka menerima permintaan HTTP dari pengguna, memproses data dari Model (`UserModel`), dan mengembalikan tanggapan yang sesuai.

Penting untuk diingat bahwa implementasi pola desain MVC dapat bervariasi tergantung pada bahasa pemrograman, platform, atau framework yang digunakan. Penerapan yang lebih lengkap dari MVC dapat melibatkan pemisahan yang lebih jelas antara Model, View, dan Controller, serta penggunaan mekanisme untuk menghubungkan ketiganya, seperti routing dan event handling.

### 5. Mampu menunjukkan dan menjelaskan konektivitas ke database
```
  import cors from 'cors'
  import dotenv from 'dotenv'
  import express, { Request, Response } from 'express'
  import mongoose from 'mongoose'
  import path from 'path'
  import { keyRouter } from './routers/keyRouter'
  import { orderRouter } from './routers/orderRouter'
  import { productRouter } from './routers/productRouter'
  import { seedRouter } from './routers/seedRouter'
  import { userRouter } from './routers/userRouter'
  
  dotenv.config()
  
  const MONGODB_URI =
    process.env.MONGODB_URI || 'mongodb://localhost/tsmernamazonadb'
  mongoose.set('strictQuery', true)
  mongoose
    .connect(MONGODB_URI)
    .then(() => {
      console.log('connected to mongodb')
    })
    .catch(() => {
      console.log('error mongodb')
    })
  
  const app = express()
  app.use(
    cors({
      credentials: true,
      origin: ['http://localhost:5173'],
    })
  )
  
  app.use(express.json())
  app.use(express.urlencoded({ extended: true }))
  
  app.use('/api/products', productRouter)
  app.use('/api/users', userRouter)
  app.use('/api/orders', orderRouter)
  app.use('/api/seed', seedRouter)
  app.use('/api/keys', keyRouter)
  
  app.use(express.static(path.join(__dirname, '../../frontend/dist')))
  app.get('*', (req: Request, res: Response) =>
    res.sendFile(path.join(__dirname, '../../frontend/dist/index.html'))
  )
  
  const PORT: number = parseInt((process.env.PORT || '4000') as string, 10)
  
  app.listen(PORT, () => {
    console.log(`server started at http://localhost:${PORT}`)
  })
```
Kode diatas merupakan file entry point untuk server Node.js dalam aplikasi web. Mari kita jelaskan setiap bagian kode tersebut:

1. Import Statement:
   - Kode di awal mengimpor modul-modul yang diperlukan untuk membangun server Node.js. Modul-modul tersebut meliputi `cors`, `dotenv`, `express`, `mongoose`, dan `path`.

2. dotenv Configuration:
   - Baris `dotenv.config()` digunakan untuk mengkonfigurasi modul `dotenv`. Modul ini digunakan untuk memuat variabel lingkungan dari file `.env` ke dalam `process.env`.

3. Mongoose Connection:
   - Bagian ini berisi konfigurasi dan koneksi ke MongoDB menggunakan modul `mongoose`. Nilai URL koneksi MongoDB diperoleh dari variabel lingkungan `MONGODB_URI` yang dibaca dari file `.env`.

4. Express Setup:
   - Membuat instance aplikasi Express menggunakan `express()`. Ini merupakan kerangka kerja web yang digunakan untuk menangani permintaan HTTP dan merespons dengan data.

5. Middleware:
   - Menggunakan middleware `cors` untuk mengizinkan permintaan dari domain yang telah ditentukan.
   - Menggunakan middleware `express.json()` untuk mengurai permintaan dengan format JSON.
   - Menggunakan middleware `express.urlencoded()` untuk mengurai permintaan dengan format URL-encoded.

6. Routers:
   - Menggunakan berbagai router yang diimpor, seperti `productRouter`, `userRouter`, `orderRouter`, `seedRouter`, dan `keyRouter`. Router ini akan menangani rute-rute yang didefinisikan dalam masing-masing file router terkait.

7. Static File and Client-Side Routing:
   - Menggunakan middleware `express.static()` untuk mengirimkan file statis seperti CSS, JavaScript, dan gambar ke klien dari direktori yang ditentukan.
   - Menggunakan middleware `app.get()` untuk menangani semua rute yang tidak ditangani oleh router sebelumnya. Dalam hal ini, mengirimkan file HTML utama ke klien untuk menangani routing di sisi klien menggunakan file `index.html` yang berada di direktori `frontend/dist`.

8. Server Listening:
   - Menentukan nomor port server dengan mengambil nilai dari variabel lingkungan `process.env.PORT` atau menggunakan nilai default `4000`.
   - Memulai server untuk mendengarkan permintaan pada port yang ditentukan.

Kode ini mengkonfigurasi server HTTP yang menggunakan Express sebagai kerangka kerja web untuk menangani permintaan dan merespons dengan data. Server ini terhubung ke MongoDB menggunakan Mongoose untuk berinteraksi dengan database. Middleware seperti CORS dan pemrosesan body permintaan diatur untuk mengatur permintaan dan respons yang diterima. Rute-rute yang dihandle oleh berbagai router juga ditentukan, serta pengaturan untuk penanganan file statis dan routing di sisi klien.

Selanjutnya, server akan mendengarkan permintaan pada port yang ditentukan dan mencetak pesan ke konsol saat server dimulai.

### 6. Mampu menunjukkan dan menjelaskan pembuatan web service dan setiap operasi CRUD nya 

### 7. Mampu menunjukkan dan menjelaskan Graphical User Interface dari produk digital

### 8. Mampu menunjukkan dan menjelaskan HTTP connection melalui GUI produk digital

### 9. Mampu Mendemonstrsikan produk digitalnya kepada publik dengan cara-cara kreatif melalui video Youtube
### 10. Bonus: Mendemonstrasikan penggunaan Machine Learning
