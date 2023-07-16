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
