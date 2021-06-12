# soal-shift-sisop-modul-4-F11-2021
Repositori kelompok F11 untuk soal shift sisop modul 4

## Soal 1
Di suatu jurusan, terdapat admin lab baru yang super duper gabut. Ia Bernama Sin. Sin baru menjadi admin di lab tersebut selama 1 bulan. Selama sebulan tersebut ia bertemu orang-oarng hebat di lab tersebut, salah satunya yaitu Sei. Sei dan Sin akhirnya berteman baik. Karena belakangan ini sedang ramai tentang kasus keamanan data, mereka berniat membuat filesystem dengan metode encode yang mutakhir. Berikut adalah filesystem rancangan Sin dan Sei.

**NOTE**

- Semua file yang berada pada direktori harus ter-encode menggunakan Atbash cipher(mirror).
Misalkan terdapat file bernama kucinglucu123.jpg pada direktori DATA_PENTING “AtoZ_folder/DATA_PENTING/kucinglucu123.jpg” → “AtoZ_folder/WZGZ_KVMGRMT/pfxrmtofxf123.jpg”

- Filesystem berfungsi normal layaknya linux pada umumnya, Mount source (root) filesystem adalah directory /home/[USER]/Downloads, dalam penamaan file ‘/’ diabaikan, dan ekstensi tidak perlu di-encode.

- Referensi : https://www.dcode.fr/atbash-cipher

### Poin Soal 1
a. Jika sebuah direktori dibuat dengan awalan “AtoZ_”, maka direktori tersebut akan menjadi direktori ter-encode. 

b. Jika sebuah direktori di-rename dengan awalan “AtoZ_”, maka direktori tersebut akan menjadi direktori ter-encode.

c. Apabila direktori yang terenkripsi di-rename menjadi tidak ter-encode, maka isi direktori tersebut akan ter-decode.

d. Setiap pembuatan direktori ter-encode (mkdir atau rename) akan tercatat ke sebuah log. Format : /home/[USER]/Downloads/[Nama Direktori] → /home/[USER]/Downloads/AtoZ_[Nama Direktori]

e. Metode encode pada suatu direktori juga berlaku terhadap direktori yang ada di dalamnya. (rekursif)

Untuk menyelesaikan soal di atas, berikut fungsi encode dan decodenya :
```C
void encode1(char* strEnc1) 
{ 
    if(strcmp(strEnc1, ".") == 0 || strcmp(strEnc1, "..") == 0)
        return;
    
    int strLength = strlen(strEnc1);
    for(int i = 0; i < strLength; i++) 
    {
	if(strEnc1[i] == '/') 
            continue;
	if(strEnc1[i] == '.')
            break;
        
	if(strEnc1[i] >= 'A' && strEnc1[i] <= 'Z')
            strEnc1[i] = 'Z' + 'A' - strEnc1[i];
        if(strEnc1[i] >= 'a' && strEnc1[i] <= 'z')
            strEnc1[i] = 'z' + 'a' - strEnc1[i];
    }
}

void decode1(char * strDec1) // decrypt encv1_
{
    if(strcmp(strDec1, ".") == 0 || strcmp(strDec1, "..") == 0 || strstr(strDec1, "/") == NULL) 
        return;
    
    int strLength = strlen(strDec1), s = 0;
    for(int i = strLength; i >= 0; i--)
    {
    	if(strDec1[i] == '/')
            break;

 	if(strDec1[i] == '.') // nyari titik terakhir
        {
	    strLength = i;
	    break;
	}
    }
    for(int i = 0; i < strLength; i++)
    {
	if(strDec1[i] == '/')
        {
	    s = i + 1;
	    break;
        }
    }
    for(int i = s; i < strLength; i++) 
    {
    	if(strDec1[i] == '/')
        {
            continue;
        }
        if(strDec1[i] >= 'A' && strDec1[i] <= 'Z')
        {
            strDec1[i] = 'Z' + 'A' - strDec1[i];
        }
        if(strDec1[i] >= 'a' && strDec1[i] <= 'z')
        {
            strDec1[i] = 'z' + 'a' - strDec1[i];
        }
    }	
}
```

Untuk mencatat setiap pembuatan direktori ter-encode, digunakan fungsi di bawah ini :
```C
void logging1(const char* old, char* new) 
{
    FILE * logFile = fopen(AtoZLogPath, "a");
    fprintf(logFile, "%s â†’ %s\n", old, new);
    fclose(logFile);
}
```

## Soal 4 
Untuk memudahkan dalam memonitor kegiatan pada filesystem mereka, Sin dan Sei membuat sebuah log system dengan spesifikasi sebagi berikut.
a. Log system yang akan terbentuk bernama “SinSeiFS.log” pada direktori home pengguna. Log system ini akan menyimpan daftar perintah system call yan gtelah dijalankan pada filesystem.

b. Karena Sin dan Sei suka kerapian, maka log yang dibuat akan dibagi menjadi dua level, yaitu INFO dan WARNING.

c. Untuk log level WARNING, digunakan untuk mencatat syscall rmdir dan unlink.

d. Sisanya akan dicatat pada level INFO.

e. Format untuk logging yaitu :
    [Level]::[dd][mm][yyyy]-[HH]:[MM]:[SS]:[CMD]::[DESC :: DESC]
