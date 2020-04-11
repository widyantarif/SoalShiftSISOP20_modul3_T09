# SoalShiftSISOP20_modul3_T09

Nama Anggota: 
  - Robby Irvine Surya 05311840000023
  - Widyantari Febiyanti 05311840000030

## Soal 4a
  - Menampilkan hasil perkalian matriks [4x2] dengan [2x5] dan ditampilkan dilayar output. 
  - Deklarasi variabel di dalam codingan. 
## Penyelesaian
Source Code: 
``` 
#include <stdio.h>
#include <pthread.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>

int matA[4][2] = {
  {4, 2},
  {2, 1},
  {1, 3},
  {3, 2}
};
int matB[2][5] = {
  {1, 2, 1, 2, 1},
  {1, 5, 1, 3, 1}
};
int matC[4][5];

struct args { 
  int i;
  int j;
};

void *kali(void* arg) { 
  int i = ((struct args*)arg)->i; 
  int j = ((struct args*)arg)->j; 

  for (int k = 0; k < 2; k++) { 
    matC[i][j] += matA[i][k] * matB[k][j]; 
  }
} 
int main() {

  pthread_t tid[4][5]; 
  for (int i = 0; i < 4; i++) {
    for (int j = 0; j < 5; j++) {
      struct args *index = (struct args *)malloc(sizeof(struct args));
      index->i = i;
      index->j = j;
      pthread_create(&tid[i][j], NULL, &kali, (void *)index);
    }
  }

  for (int i = 0; i < 4; i++) { 

    for (int j = 0; j < 5; j++) {
      pthread_join(tid[i][j], NULL);
    }
  }

  printf("Matriks :\n"); 
  for (int i = 0; i < 4; i++) {
    for (int j = 0; j < 5; j++) {
      printf("%4d", matC[i][j]);
    }
    printf("\n");
  }
 
  key_t key = 1337;
  int *value;

  int shmid = shmget(key, sizeof(matC), IPC_CREAT | 0666);
  value = shmat(shmid, NULL, 0);

  int* p = (int *)value;

  memcpy(p, matC, 80);

  shmdt(value);
}
```

- ```int matA[4][2] = { {4, 2}, {2, 1}, {1, 3}, {3, 2} };``` 
  adalah fungsi untuk mendeklarasikan variabel untuk Matriks A yang ber-ordo 4x2
- ```int matB[2][5] = { {1, 2, 1, 2, 1}, {1, 5, 1, 3, 1} };```
  adalah fungsi untuk mendeklarasikan variabel untuk Matriks B yang ber-ordo 2x5
- ```int matC[4][5];``` 
  adalah fungsi untuk mendeklarasikan ordo Matriks C yaitu 4x5
  
-  ```struct args { int i; int j }; ``` 
  adalah fungsi membuat struct argumen atau kumpulan variabel argumen. ```int i``` berguna untuk menunjukkan baris dalam matriks    sedangkan ```int j``` berguna untuk menunjukkan kolom dalam matriks. 
  
``` 
   void kali*(void arg) {
   int i = ((struct args*)arg)->i;
   int j = ((struct args*)arg)->j;
```
- Adalah fungsi untuk memanggil perkalian untuk struct yang sudah dideklarasikan diatas.

```
  for (int k = 0; k < 2; k++) { 
    matC[i][j] += matA[i][k] * matB[k][j]; 
  }
```
- Adalah fungsi looping untuk perkalian matriks A dan B. ```matC[i][j] += matA[i][k] * matB[k][j]``` 
memiliki arti yaitu: Perkalian Matriks A ber-ordo [4x2] dengan Matriks B ber-ordo [2x5] akan menghasilkan sebuah Matriks C ber-ordo [4x5]. 

```
int main() {
pthread_t tid[4][5];
  for (int i = 0; i < 4; i++) {
    for (int j = 0; j < 5; j++) {
      struct args *index = (struct args *)malloc(sizeof(struct args));
      index->i = i;
      index->j = j;
```
- ```pthread_t tid[4][5];``` adalah fungsi main yang berfungsi untuk menunjukkan array dari thread bernama tid yang memiliki ukuran 4x5 (sesuai dengan hasil perkalian) 

```
for (int i = 0; i < 4; i++) 
{for (int j = 0; j < 5; j++) {
``` 
- berfungsi sebagai sistem looping untuk setiap thread yang akan dibuat. 


``` 
      struct args *index = (struct args *)malloc(sizeof(struct args));
      index->i = i;
      index->j = j;
```
- fungsi  ```struct args *index = (struct args *)malloc(sizeof(struct args));``` memiliki arti yaitu akan membuat sebuah nilai/objek bernama struct args (argumen) serta index dan akan mengalokasikan memori sesuai dengan struct ags. 
- fungsi ```index->i = i;``` dan ```index->j = j;``` memiliki arti yaitu akan mengatur nilai pada objek dari struct ags bernama index dan bernilai i dan j. 

```
  pthread_create(&tid[i][j], NULL, &kali, (void *)index)
  }
  }

  for (int i = 0; i < 4; i++) { //gabungin threadnya

    for (int j = 0; j < 5; j++) {
      pthread_join(tid[i][j], NULL);
    }
  }
```

- fungsi ```pthread_create(&tid[i][j], NULL, &kali, (void *)index)``` memiliki arti yaitu untuk membuat thread tid pada nilai i dan nilai j dengan menggunakan fungsi kali dan parameter index. 
- fungsi ```pthread_join(tid[i][j], NULL);``` memiliki arti yaitu untuk menggabungkan semua thread yang telah dibuat. 

``` printf("Matriks :\n");
    for (int i = 0; i < 4; i++) {
      for (int j = 0; j < 5; j++) {
        printf("%4d", matC[i][j]);
      }
    printf("\n");
    }
``` 
- adalah fungsi untuk menampilkan hasil perkalian matriks. Yaitu hasil perkalian Matriks A dengan Matriks B yang akan menjadi Matriks C yang memiliki ordo [4x5]

```
key_t key = 1337;
  int *value;
  int shmid = shmget(key, sizeof(matC), IPC_CREAT | 0666);
  value = shmat(shmid, NULL, 0);

  int* p = (int *)value;
  memcpy(p, matC, 80);
  shmdt(value);
```
- berikut adalah fungsi untuk melakukan shared memory. Shared memory adalah memori yang dapat diakses secara bersamaan oleh beberapa program  dengan maksud untuk menyediakan komunikasi di antara mereka atau menghindari salinan yang berlebihan. ```key_t key = 1337;``` memiliki arti yaitu membuat Shared Memory ID dengan key yaitu 1337. 

- fungsi ```int shmid = shmget(key, sizeof(matC), IPC_CREAT | 0666);``` memiliki arti yaitu akan membuat Shared Memori shmid dengan ukuran sesuai dengan Matriks C dengan private key dan pada memori berbagi atau Shared Memori menggunakan kode 0666 yang berarti mengizinkan adanya read and write dalam penggunaannya. 
- fungsi ```value= shmat(shmid, NULL, 0);``` memiliki arti ```shmat``` adalah melampirkan segmen Shared Memori yang terkait dengan Shared Memory ID, shmid ke segmen data dari proses panggilan. 


## Soal 4c
  - Membuat suatu program menggunakan IPC Pipes untuk mengetahui jumlah file dan folder di direktori saat ini dengan command "ls | wc -l"

## Penyelesaian
Source Code: 
```
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<sys/types.h>
#include<string.h>
#include<sys/wait.h>

int main() {
  int fd[2];

  pid_t pid;

  pipe(fd);

  pid = fork();
  if (pid == 0) {
    dup2(fd[1], 1);
    close(fd[0]);
    char *argv[] = {"ls", NULL};
    execv("/bin/ls", argv);
  }
  while(wait(NULL) > 0);

  dup2(fd[0], 0);
  close(fd[1]);
  char *argv[] = {"wc", "-l", NULL};
  execv("/usr/bin/wc", argv);

}
```

```
int main() {
int fd[2];
pid_t pid;
pipe(fd);
```
- Memiliki arti yaitu untuk inisiasi fungsi main. ```int fd[2]``` berfungsi untuk inisiasi bahwa dalam proses ini akan membutuhkan 2 file descriptor. 
- ```pid_t pid``` adalah inisiasi proses ID yang bernama pid. proses id ini berfungsi untuk mengalokasikan setiap proses saat dibuat. 
- ```pipe(fd);``` adalah fungsi yang menjelaskan bpipes hanya memiliki 2 ujung yaitu ujung 1 dan 0. 1 untuk write sedangkan 0 untuk read. sesuai dengan file descriptornya. 

```
pid = fork();
  if (pid == 0) {
    dup2(fd[1], 1);
    close(fd[0]);
    char *argv[] = {"ls", NULL};
    execv("/bin/ls", argv);
  }
  while(wait(NULL) > 0);

  dup2(fd[0], 0);
  close(fd[1]);
  char *argv[] = {"wc", "-l", NULL};
  execv("/usr/bin/wc", argv);

}
```
- command ```dup2(fd[1], 1);``` memiliki fungsi untuk mencopy hasil output. 
- command ```close(fd[0]);``` memilliki fungsi untuk menclose dan menread hasil output.
- command ```execv("/bin/ls", argv);``` memiliki fungsi untuk menampilkan hasil read dari semua file yang ada didalam directory. 
- command ```dup2(fd[0], 0);``` memiliki fungsi untuk mencopy hasil output dari nilai pipes yang dimasukkan. 
- command ```close(fd[1]);```memiliki fungsi close dan menwrite hasil output.
- command ```char *argv[] = {"wc", "-l", NULL};``` memiliki fungsi untuk Word Count yaitu menampilkan jumlah file yang ada didalam directory. 
- command ```execv("/usr/bin/wc", argv);``` memiliki fungsi Word Count akan membaca per baris, dan mengeluarkan output bernilai angka dan menampilkan jumlah file yang ada didalam directory. 
