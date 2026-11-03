# Simulator Rangkaian Dasar

Trainer digital kelistrikan otomotif untuk siswa SMK TKR. Web statis HTML/CSS/SVG/Vanilla JavaScript, tanpa instalasi, dependency, server aplikasi, atau proses build. Tampilan memakai tema workbench gelap; relay dimodelkan sebagai SPDT 5 pin.

## Membuka dan menggunakan

Klik dua kali `index.html` untuk menjalankan melalui `file://`. Default: mode Kustom, Lampu Pijar 12 V, 1 lampu.

1. Pilih Kustom, Sistem Starter, Sistem Penerangan, Sistem Tanda Belok, atau Sistem Pengisian pada menu Mode Rangkaian. Pergantian mode menghapus semua kabel untuk mencegah sambungan lama terbawa ke sistem lain.
2. Mode Kustom: pilih LED 12 V atau Lampu Pijar 12 V, lalu tambahkan/kurangi lampu, dinamo, dan klakson. Batas gabungan empat beban hanya berlaku di mode ini.
3. Pilih kabel merah/hitam, pilih terminal pertama lalu terminal tujuan. Komponen mode tidak memiliki kabel awal; seluruh sambungan dibuat manual. Memilih terminal pertama lagi membatalkan pilihan.
4. Klik/tap badan switch pada gambar rangkaian untuk ON/OFF. Tombol starter, selector dimmer, saklar rem, dan selector sein juga dioperasikan langsung pada gambar; keyboard Enter/Spasi tersedia untuk kontrol tersebut.
5. Undo Kabel di header menghapus kabel terakhir. Reset mengembalikan mode Kustom, satu Lampu Pijar 12 V, switch OFF, visualisasi OFF, dan seluruh kabel kosong.
6. Visualisasi Arus di sidebar menampilkan jalur konvensional dari baterai positif menuju negatif. Tombol ini hanya mengubah tampilan; rangkaian tetap dihitung ketika visualisasi OFF.

LED berpolaritas (A positif/B negatif); pijar, dinamo, dan klakson non-polar. Mengubah jenis lampu tidak menghapus kabel. Mengurangi beban terakhir dari jenisnya menghapus kabel yang terhubung ke komponen itu. Switch utama mengendalikan jalur rangkaian/coil relay. Dinamo yang mendapat jalur tertutup memutar kipasnya; klakson yang mendapat jalur tertutup memainkan nada dua frekuensi sintetis melalui Web Audio.

## Mode sistem sepeda motor

- **Starter:** baterai, fuse, kunci kontak pada switch utama, relay SPDT 5 pin, tombol starter bergambar, dan dinamo starter. Tombol starter pada workspace mengendalikan terminal kontaknya.
- **Penerangan:** lampu utama dekat dan jauh, lampu posisi depan, lampu belakang, lampu rem, lampu panel meter, lampu plat nomor, baterai, fuse, switch utama, relay 5 pin, selector dimmer tiga posisi (OFF/LOW/HIGH), dan saklar rem. Posisi dimmer menentukan kontak yang tersambung ke filamen dekat atau jauh; lampu rem memerlukan saklarnya ON.
- **Tanda Belok:** flasher, sein depan/belakang kiri dan kanan, indikator panel, switch utama, dan selector bergambar (OFF/kiri/kanan/hazard). Lampu sein yang mendapat jalur tertutup berkedip.
- **Pengisian:** stator/generator, regulator-rectifier (kiprok), indikator pengisian, fuse, dan baterai.

Mode menyediakan simbol/terminal komponen tanpa kabel bawaan. Saklar tambahan dapat diklik pada gambar di workspace setelah terminal-terminalnya dirangkai. Model starter, penerangan, dan sein mengikuti konektivitas topologi simulator. Mode pengisian menampilkan koneksi antarkomponen untuk latihan, tetapi belum menghitung pembangkitan AC, penyearahan, tegangan, atau arus pengisian fisik.

Relay menggunakan kontak pindah SPDT 5 pin: pin 30 adalah common, pin 85/86 adalah coil, pin 87 adalah normally open (NO), dan pin 87a adalah normally closed (NC). Saat coil mati, kontak 30–87a tersambung. Saat coil aktif, kontak berpindah ke 30–87. Pin 87a sekarang menjadi terminal yang dapat dipilih untuk wiring.

## Cakupan model

Engine V2 menilai konektivitas lintasan tertutup, termasuk rangkaian seri/paralel. Konduktor ideal dibedakan dari load. Short hanya melalui konduktor. Belum ada solver jaringan resistansi, pengukuran voltage drop/besar arus, atau fuse putus.

Lampu pijar identik pada satu string seri sederhana memakai resistansi tetap: arus relatif 1/n, sehingga daya relatif tiap lampu `relativePower = 1/n²`. Nilai untuk 1–4 lampu adalah 1, 0,25, 0,1111, dan 0,0625. n berasal dari beban yang benar-benar ada dalam lintasan seri aktif, bukan jumlah lampu di workspace. Cabang paralel langsung pada baterai masing-masing mendapat daya relatif 1. String seri independen yang diparalel pada sumber juga didukung.

Efek tampilan memakai `visualIntensity = relativePower ** 0.4`; nilai daya tidak diubah. Ini pendekatan pembelajaran equal-resistance, bukan prediksi tepat cahaya lampu nyata yang resistansinya berubah terhadap temperatur. Percabangan pada net perantara atau campuran dengan coil memerlukan solver: lampu tetap ditentukan ON/OFF melalui konektivitas, dengan `relativePower: null`, `brightnessModel: unsupported-mixed-nominal`, dan terang visual nominal 1. LED tetap directional dan tidak memakai rumus pijar.

`activePaths` menyediakan urutan lintasan representatif dan bagian net beserta cabang alternatif; `activeSegments` menyediakan cakupan edge pada lintasan valid. Ini data topologi, bukan pembuktian besar/arah arus fisik pada setiap cabang net ideal. Konfigurasi relay yang berosilasi tanpa keadaan stabil didiagnosis dan output load ditahan OFF; osilasinya tidak dianimasikan.

Visualisasi memakai semua ordered representative `activePaths`. Wire/current berada di bawah body komponen, overlay internal di atas artwork, dan socket/highlight paling atas. Socket relay berada di luar housing dengan lead internal; overlay kabel menyalin `d` base cable persis; overlay internal memakai geometry lokal reusable untuk fuse, switch, kontrol starter/dimmer/rem/sein, coil, kontak relay, dan semua load mode. Geometry internal diproyeksikan melalui SVG screen matrix agar ujungnya tetap berada di pusat terminal saat resize. Animasi CSS dash 6/18 bergerak 24px per 0,8 detik, dengan arah dan phase berdasarkan urutan segmen. Tidak ada loop animasi JavaScript per frame. Cyan #00bdd6 menandai jalur beban, electric blue #209fe8 jalur kontrol (overlay 2px); warna kabel asli tetap.

Segmen bersama ditampilkan sekali dengan arah/phase lintasan valid pertama. Cabang konduktor alternatif di dalam net ideal tidak memiliki pembagian arus terhitung; ilustrasi memakai rute representatif engine. Routing lanes berbelok halus dipertahankan, termasuk stub beban dinamis nominal 18px ke bawah lalu keluar horizontal menjauhi label. Pencarian jalur memprioritaskan gerak vertikal searah menuju target; pembalikan hanya diizinkan jika obstacle menghalangi jalur searah. Lane berdasarkan endpoint dan occupancy bersama merah/hitam, dengan alternatif berjarak 6px; tidak lagi memakai counter lane yang terus memanjang. Heuristik ini tidak menjamin seluruh crossing hilang; kabel yang berbagi terminal dapat berbagi stub pendek.

Short circuit menekan seluruh overlay normal dan menampilkan peringatan. Switch terbuka, kontak relay NO/NC, serta LED terbalik mengikuti state electrical saat menentukan jalur aktif. Preferensi reduced motion menghasilkan highlight statis. Desktop memakai sidebar kanan, workspace setinggi sisa viewport, dan kabel 2,85px; terminal tetap mempunyai touch target sedikitnya 44px. Tablet landscape mempertahankan board yang dapat digulir dan sidebar ringkas; portrait perangkat sentuh menampilkan pesan rotasi.

Layout memakai bidang SVG 1280x840. Relay bergeser ke tengah/atas (510,335), baterai ke kiri/bawah (210,665) menjauhi fuse. Fuse tetap satu kelompok di kiri tengah dan switch utama di kanan bawah. Bank mode otomatis menata hingga tujuh komponen penerangan. Kontrol tambahan ditempatkan di area kanan workspace. Mode Kustom tetap membatasi empat beban gabungan lampu, dinamo, dan klakson. Sidebar memakai medium cards sekitar 74px; Undo SVG 22px tetap di header.

## Docking dan routing

Metadata visual docking terpisah dari graph: semua beban mode DOWN, selector bersisi kiri/kanan, baterai UP, fuse dan relay sesuai sisi socket, switch utama LEFT. Anchor tetap pusat lingkaran terminal asli, bukan touch target. Socket relay 30/85 di luar kiri housing, 87/86 di luar kanan, dan 87a di atas housing. Cap visual tanpa ID/handler digambar di atas layer arus; terminal asli tetap menjadi target keyboard/touch. Endpoint/current dihitung ulang saat resize; topologi tidak menyimpan koordinat pixel.

Router memakai kandidat L/Z/corridor deterministic dengan rounded corners, tanpa grid pathfinding. Occupancy horizontal dan vertikal memeriksa jarak lane sekaligus interval overlap. Default center spacing 6px, base wire 2,85px (clearance nominal 3,15px). Segmen dengan interval terpisah boleh menggunakan lane sama. Kandidat sederhana/monotonic diprioritaskan; obstacle dan overlap memengaruhi pemilihan jalur. Common socket masih dapat berbagi stub pendek; heuristic tidak menjamin seluruh crossing hilang.

Tooltip terminal custom/native, Cek Rangkaian/diagnosis manual, dan badge tahap telah dihapus. Label edukatif dan ARIA tetap. Status bar mempertahankan feedback wiring, short dan relay tidak stabil. Feedback LED terbalik bersifat konservatif: hanya jika pelonggaran arah satu LED menghasilkan lintasan sumber-return yang melewatinya terbalik. Pemeriksaan ini tidak mengubah state electrical. Simulator tetap trainer eksploratif tanpa penilaian benar/salah.
## Dokumentasi dan pengujian

- [Brief](docs/PROJECT_BRIEF.md): tujuan project; bagian cakupan awal adalah catatan tahap persiapan.
- [Status aktual](docs/PROJECT_STATUS.md)
- [Checkpoint task](docs/CURRENT_TASK.md)
- [Riwayat append-only](docs/DEVELOPMENT_LOG.md)
- [Hasil regression](docs/REGRESSION_RESULTS.json): 52 pemeriksaan per viewport, 1366×768 dan 1920×1080; termasuk console error capture di Edge headless melalui file lokal.
- [Regression Tahap 6](docs/REGRESSION_STAGE_6.json): layout desktop, wiring, brightness, relay, current paths, resize, reduced motion, dan emulasi tablet. Browser/perangkat fisik lain belum diuji.
- [Regression Tahap 6.5](docs/REGRESSION_STAGE_6_5.json): 46 pemeriksaan per desktop, sweep 40 jalur lampu per viewport, koridor antar-komponen, dialog/keyboard, dan perbandingan source engine/current.
- [Regression Stage 6.6](docs/REGRESSION_STAGE_6_6.json): docking, socket/body, default/reset, serta 31 regression per desktop.
- [Regression Stage 7](docs/REGRESSION_STAGE_7.json): mengulang 31 regression inti dan 33 pemeriksaan diagnosis/UI per desktop, termasuk 10 kasus diagnosis, read-only state, keyboard, Detail, dan resize.
- [Regression Stage 7.1](docs/REGRESSION_STAGE_7_1.json): 38 pemeriksaan per desktop, routing matrix/docking/occupancy dua sumbu, cleanup UI, regresi fungsi, engine unchanged, Escape/reduced motion dan emulasi tablet/portrait lulus; tidak ada error JavaScript. Bukti Stage 7 di atas adalah riwayat fitur yang kemudian dibersihkan pada 7.1.
- [Aturan development](AGENTS.md)

Source aplikasi hanya `index.html`, `style.css`, dan `script.js`. `assets/reference-ui.png` hanya referensi desain dan tidak dimuat sebagai UI/background. Project dapat di-host sebagai file statis di GitHub Pages; belum dipublikasikan.



