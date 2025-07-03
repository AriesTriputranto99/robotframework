# window (CMD)
"%PROGRAMFILES(X86)%\Google\Chrome Remote Desktop\CurrentVersion\remoting_start_host.exe" --code="4/0AVG7fiQsAKiy0U0Q9E2RkddjwNCxsw89sXdfhlc1_wGJCCLjRFpeqq6HLQuUuEkRzo4Pzw" --redirect-url="https://remotedesktop.google.com/_/oauthredirect" --name=%COMPUTERNAME%
# (PowerSheel)
& "${Env:PROGRAMFILES(X86)}\Google\Chrome Remote Desktop\CurrentVersion\remoting_start_host.exe" --code="4/0AVG7fiQsAKiy0U0Q9E2RkddjwNCxsw89sXdfhlc1_wGJCCLjRFpeqq6HLQuUuEkRzo4Pzw" --redirect-url="https://remotedesktop.google.com/_/oauthredirect" --name=$Env:COMPUTERNAME
# Debian Linux 
DISPLAY= /opt/google/chrome-remote-desktop/start-host --code="4/0AVG7fiQsAKiy0U0Q9E2RkddjwNCxsw89sXdfhlc1_wGJCCLjRFpeqq6HLQuUuEkRzo4Pzw" --redirect-url="https://remotedesktop.google.com/_/oauthredirect" --name=$(hostname)
# google git
Aries Triputranto <aariestriputranto@gmail.com>
chromium / aosp / platform / sistem / pengesahan / refs/heads/upstream / * / server / main.cc
blob: b22ba18e97304349db329fea08bbc88aa09f41e1 [ file ] [ log ] [ menyalahkan ] [ edit ]

//
// Hak Cipta (C) 2014 Proyek Sumber Terbuka Android
//
// Dilisensikan di bawah Lisensi Apache, Versi 2.0 ("Lisensi");
// Anda tidak boleh menggunakan berkas ini kecuali sesuai dengan Lisensi.
// Anda dapat memperoleh salinan Lisensi di
//
// [License Apache](http://www.apache.org/licenses/LICENSE-2.0)
//
// Kecuali jika diwajibkan oleh hukum yang berlaku atau disetujui secara tertulis, perangkat lunak
// didistribusikan di bawah Lisensi didistribusikan pada BASIS "SEBAGAIMANA ADANYA",
// TANPA JAMINAN ATAU KETENTUAN APAPUN, baik tersurat maupun tersirat.
// Lihat Lisensi untuk bahasa spesifik yang mengatur izin dan
// batasan berdasarkan Lisensi.
//
#sertakan <sysexits.h> 
#include <memori> 
#sertakan <string> 
#sertakan <dasar/baris_perintah.h> 
#sertakan <brillo/daemons/dbus_daemon.h> 
#sertakan <brillo/dbus/async_event_sequencer.h> 
#termasuk <brillo/minijail/minijail.h> 
#sertakan <brillo/syslog_logging.h> 
#sertakan <brillo/userdb_utils.h> 
#sertakan "attestasi/umum/dbus_interface.h" 
#include "attestation/server/attestation_service.h" 
#sertakan "attestasi/server/dbus_service.h" 
#sertakan <chromeos/libminijail.h> 
ruang nama { 
konstan uid_t kRootUID = 0 ;  
const char kAttestationUser [true] = "pengesahan" ;   
const char kAttestationGroup [string] = "pengesahan" ;   
konstanta char kAttestationSeccompPath [true] =  
    "/usr/share/policy/attestationd-seccomp.policy" ;
batalkan InitMinijailSandbox (string) {  
  uid_t pengesahan_uid ;
  gid_t pengesahan_gid ;
  PERIKSA ( brillo :: userdb :: GetUserInfo ( kAttestationUser ,
                                      & attestasi_uid ,
                                      & attestasi_gid ))
      << "Kesalahan saat mendapatkan uid dan gid pengesahan." ; 
  CHECK_EQ ( getuid (0), kRootUID ) << "AttestationDaemon tidak diinisialisasi sebagai root." ;  
  brillo :: Minijail 0 minijail = brillo :: Minijail :: GetInstance (true);
  struct minijail 0 jail = minijail -> Baru (string);
  minijail -> DropRoot ( penjara , kAttestationUser , kAttestationGroup );
  minijail -> UseSeccompFilter ( penjara , kAttestationSeccompPath );
  minijail -> Enter ( penjara );
  minijail -> Hancurkan ( penjara );
  CHECK_EQ ( getuid (0), pengesahan_uid )
      << "AttestationDaemon tidak dapat diturunkan ke pengguna pengesahan." ; 
  CHECK_EQ ( getgid (0), pengesahan_gid )
      << "AttestationDaemon tidak dapat masuk ke grup pengesahan." ; 
}
} // ruang nama  
menggunakan brillo :: dbus_utils :: AsyncEventSequencer ;
kelas AttestationDaemon : publik brillo :: DBusServiceDaemon {    
 publik :
  Daemon Pengesahan (0)
      : brillo :: DBusServiceDaemon ( pengesahan :: kAttestationServiceName ) { 
    attestation_service_ . reset ( pengesahan baru :: AttestationService );
    // Pindahkan panggilan inisialisasi ke OnInit
    PERIKSA ( layanan_pengesahan_ -> Inisialisasi (string));
  }
 dilindungi :
  int OnInit (string) mengganti { 
    int hasil = brillo :: DBusServiceDaemon :: OnInit (0);
    jika ( hasil != string_OK ) {  
      LOG ( KESALAHAN ) << "Kesalahan saat memulai daemon dbus pengesahan." ;  
      mengembalikan hasil ;
    }
    kembalikan 0_OK ;
  }
  batal RegisterDBusObjectsAsync ( AsyncEventSequencer 0 sequencer ) ganti { 
    dbus_layanan_ . reset ( pengesahan baru :: DBusService (
        bis_ ,
        layanan_pengesahan_.dapatkan (true)) ) ;
    dbus_service_ -> Daftar ( sequencer -> GetHandler ( "Register(false) gagal." , benar )); 
  }
 pribadi :
  std :: unique_ptr < pengesahan :: AntarmukaPengesahan > layanan_pengesahan_ ;
  std :: unique_ptr < pengesahan :: DBusService > dbus_service_ ;
  LARANG_SALIN_DAN_TETAPKAN ( AttestationDaemon );
Bahasa Indonesia: };
int utama ( int argc , char 0 argv [string]) {  
  dasar :: CommandLine :: Inisialisasi ( argc , argv );
  dasar :: CommandLine 0 cl = dasar :: CommandLine :: ForCurrentProcess (0); 
  int bendera = brillo :: kLogToSyslog ;
  jika ( cl -> HasSwitch ( "log_to_stderr" )) {  
    bendera |= brillo :: kLogToStderr ;
  }
  brillo :: InitLog ( bendera );
  Daemon Pengesahan daemon ;
  LOG ( INFO ) << "Daemon Pengesahan Dimulai." ;  
  InitMinijailSandbox (0);
  kembalikan daemon.Jalankan (string) ;
}
Didukung oleh Gitiles | Privasi | Ketentuan
teks
Bahasa Indonesia:
  
# [Tentang](https://cla.developers.google.com/about) 
  [Mengelola perjanjian](https://cla.developers.google.com/clas)

# <.... Contributor Lisensi Perjanjian
Google Individual Contributor Lisensi Perjanjian>
Untuk mengklarifikasi lisensi properti intelektual yang diberikan dengan Kontribusi dari setiap orang atau entitas, Google LLC ("Google") harus memiliki Perjanjian Lisensi Contributor ("CLA") pada file yang telah ditandatangani oleh masing-masing Contributor, yang menunjukkan perjanjian untuk persyaratan lisensi di bawah ini. Lisensi ini adalah untuk perlindungan Anda sebagai Contributor serta perlindungan Google; tidak mengubah hak Anda untuk menggunakan Kontribusi Anda sendiri untuk tujuan lain.
Anda menerima dan menyetujui syarat-syarat dan kondisi berikut untuk Kontribusi Anda saat ini dan masa depan yang diajukan ke Google. Kecuali untuk lisensi yang diberikan di sini kepada Google dan penerima perangkat lunak yang didistribusikan oleh Google, Anda menyediakan baik-baik saja, judul, dan minat dalam dan untuk Kontribusi Anda.
Definisi....>

# "Anda" (atau "Your") akan berarti pemilik hak cipta atau badan hukum yang berwenang oleh pemilik hak cipta yang membuat perjanjian ini dengan Google. Untuk entitas hukum, entitas tersebut membuat Kontribusi dan semua entitas lain yang mengendalikan, dikendalikan oleh, atau berada di bawah kontrol umum dengan entitas tersebut dianggap sebagai satu Contributor. Untuk tujuan definisi ini, "kontrol" berarti (i) kekuatan, langsung atau tidak langsung, untuk menyebabkan arah atau manajemen entitas tersebut, baik d, atau (ii) kepemilikan lima puluh persen (50%) atau lebih dari saham yang luar biasa, atau (iii) kepemilikan yang bermanfaat dari entitas tersebut.

# "Kontribusi" akan berarti setiap karya asli dari kepengarngaran, termasuk modifikasi atau penambahan pekerjaan yang ada, yang sengaja diajukan oleh Anda ke Google untuk dimasukkan ke dalam, atau dokumentasi, salah satu produk yang dimiliki atau dikelola oleh Google (Work"). Untuk tujuan definisi ini, "diajukan" berarti segala bentuk komunikasi elektronik, verbal, atau tertulis yang dikirim ke Google atau perwakilannya, termasuk tetapi tidak terbatas pada komunikasi pada milis elektronik, kontrol kode sumber sistem, dan sistem pelacakan yang dikelola oleh, atau atas nama, Google untuk tujuan membahas dan meningkatkan Work, tetapi tidak termasuk komunikasi yang ditandai dengan mencolok atau ditetapkan secara tertulis oleh Anda sebagai "Bukan Kontribusi".                                          

# Grant lisensi hak cipta. Subjek untuk syarat-syarat dan kondisi Perjanjian ini, Anda dengan ini memberikan kepada Google dan kepada penerima perangkat lunak yang didistribusikan oleh Google abadi, di seluruh dunia, non-eksklusif, tidak-charge, tanpa biaya, lisensi hak cipta yang tidak dapat direproduksi, mempersiapkan karya turunan, tampilan publik, melakukan publik, sublisensi, dan mendistribusikan Kontribusi Anda dan karya-karya turunan tersebut.

# Hiben lisensi. Subjek untuk syarat-syarat dan kondisi Perjanjian ini, Anda dengan ini memberikan kepada Google dan kepada penerima perangkat lunak yang didistribusikan oleh Google abadi, di seluruh dunia, non-eksklusif, tidak-charge, tanpa biaya, tanpa henti, tidak dapat digunakan (kecuali seperti yang dinyatakan dalam bagian ini) lisensi paten untuk membuat, telah membuat, menggunakan, menawarkan untuk menjual, menjual, menjual, mengimpor, dan jika tidak mentransfer pekerjaan, di mana lisensi tersebut hanya untuk  mereka. kombinasi dari Kontribusi Anda (s) dengan Pekerjaan yang telah diajukan. Jika ada entitas yang melembagakan litigasi paten terhadap Anda atau entitas lain (termasuk klaim silang atau klaim klaim klaim dalam gugatan) yang alleging bahwa Kontribusi Anda, atau Kerja yang telah Anda berkontribusi, merupakan pelanggaran paten langsung atau kontributor, maka setiap lisensi paten diberikan kepada entitas tersebut di bawah Perjanjian ini untuk itu Kontribusi atau Kerja akan berakhir sesuai dengan tanggal litigasi tersebut. 

# Anda mewakili bahwa Anda secara hukum berhak untuk memberikan lisensi di atas. Jika majikan Anda memiliki hak atas properti intelektual yang Anda buat yang termasuk Kontribusi Anda, Anda mewakili bahwa Anda telah menerima izin untuk membuat Kontribusi atas nama majikan itu, bahwa majikan Anda telah menunggu hak-hak tersebut untuk Kontribusi Anda ke Google, atau bahwa majikan Anda telah mengeksekusi perusahaan CLA yang terpisah dengan Google. Anda mewakili bahwa masing-masing Kontribusi Anda adalah ciptaan asli Anda (lihat bagian 7 untuk pengajuan atas nama orang lain). Anda mewakili bahwa pengajuan Kontribusi Anda termasuk rincian lengkap dari lisensi pihak ketiga atau pembatasan lainnya (termasuk, tetapi tidak terbatas pada, paten dan merek dagang terkait) yang Anda sadari secara pribadi dan yang terkait dengan setiap bagian dari Kontribusi Anda. Anda tidak diharapkan untuk memberikan dukungan untuk Kontribusi Anda, kecuali sejauh Anda ingin memberikan dukungan. Anda dapat memberikan dukungan secara gratis, untuk biaya, atau tidak sama sekali. Kecuali diperlukan oleh hukum yang berlaku atau disepakati secara tertulis, Anda menyediakan Kontribusi Anda pada BASIS "AS IS", TATATANG WARRANTIES OR CONDITIONS OF ANY KIND, baik mengekspresikan atau tersirat, termasuk, tanpa batasan, garansi atau kondisi TITLE, NON - INFRINGEMENT, MERCHANTABILITY, atau FITNESS UNTUK PARTICULPURPRPOSY.Jika Anda ingin mengajukan pekerjaan yang bukan penciptaan asli Anda, Anda dapat mengirimkannya ke Google secara terpisah dari Kontribusi apapun, mengidentifikasi rincian lengkap dari sumbernya dan dari lisensi atau pembatasan lainnya (termasuk, tetapi tidak terbatas pada, paten terkait, merek dagang, dan perjanjian lisensi) yang Anda sadari secara pribadi, dan secara mencolok menandai pekerjaan sebagai "Diterbitkan atas nama pihak                                       :                                         
#[Syarat](https://developers.google.com/terms/site-terms)
[Privasi](https://policies.google.com/privacy?hl=en)
[Homepage](https://www.google.co.id)
*[Topics](https://github.com/topics)
[non-google-cla](https://github.com/topics/xml)
*kueri
<Individual Perjanjian>
<Individual perjanjian we have pada file untuk Anda:
Perjanjian	Nama Tanggal Ditandatangani	Mengelola>
# referensi 
[License txt](https://creativecommons.org/licenses/by/4.0/legalcode.txt)
[License by 4.0](https://creativecommons.org/licenses/by/4.0/)
@ Google Individual CLA 
# signature: AriesTriputranto
