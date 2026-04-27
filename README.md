## Test Results (Via GUI)
### /all-students
![gui all-student.png](images/gui%20all-student.png)  
### /all-student-name
![gui all-student-name.png](images/gui%20all-student-name.png)  
### /highest-gpa
![gui highest-gpa.png](images/gui%20highest-gpa.png)

## Test Results (Via CMD)
### /all-students
![cmd all-student.png](images/cmd%20all-student.png)  
### /all-student-name
![cmd all-student-name.png](images/cmd%20all-student-name.png)  
### /highest-gpa
![cmd highest-gpa.png](images/cmd%20highest-gpa.png)
  
## OPTIMIZATION
### CPU Time Before
![cpu before.png](images/cpu%20before.png)
  
### CPU Time After
![cpu after.png](images/cpu%20after.png)
  
### JMeter Results After Optimization
**/all-students**
![after opt all student.png](images/after%20opt%20all%20student.png)  
**/all-student-name**
![after opt all student name.png](images/after%20opt%20all%20student%20name.png)  
**/highest-gpa**
![after opt highest gpa.png](images/after%20opt%20highest%20gpa.png)  

### Conclusion
Berdasarkan hasil profiling menggunakan IntelliJ Profiler, optimasi yang dilakukan berhasil menurunkan CPU time secara signifikan pada ketiga endpoint. Penurunan drastis ini disebabkan oleh eliminasi query berulang ke database yang sebelumnya terjadi untuk setiap student, digantikan dengan satu query yang mengambil seluruh data sekaligus. Hasil pengukuran ulang menggunakan JMeter setelah optimasi juga menunjukkan penurunan sample time yang signifikan dibandingkan pengukuran awal, yang mengkonfirmasi bahwa perbaikan pada level kode berdampak nyata pada performa aplikasi secara end-to-end. Dengan demikian, dapat disimpulkan bahwa proses profiling dengan IntelliJ Profiler sangat efektif dalam mengidentifikasi bottleneck yang tidak terlihat hanya dari membaca kode, dan optimasi yang tepat sasaran mampu memberikan peningkatan performa yang jauh melebihi target minimum.

## REFLECTION  

1. JMeter digunakan untuk performance testing dari sisi luar aplikasi, mensimulasikan banyak user yang mengakses endpoint secara bersamaan dan mengukur response time serta throughput secara keseluruhan. IntelliJ Profiler bekerja dari dalam aplikasi, menunjukkan secara detail method mana yang mengonsumsi CPU time paling banyak. JMeter memberi tahu bahwa ada masalah performa, sedangkan IntelliJ Profiler membantu menemukan di mana tepatnya masalah itu berada.

2. Profiling memungkinkan kita melihat CPU time per method secara granular, sehingga tidak perlu menebak-nebak bagian mana dari kode yang lambat. Dalam tutorial ini, IntelliJ Profiler langsung menunjukkan bahwa `getAllStudentsWithCourses()` mengonsumsi CPU time jauh lebih besar dibanding method lainnya, sehingga jelas method itulah yang harus dioptimasi lebih dulu.

3. Ya, sangat efektif. Fitur Method List dengan kolom CPU time memudahkan identifikasi bottleneck secara langsung tanpa harus membaca seluruh kode satu per satu. Fitur comparison view juga sangat membantu untuk membuktikan secara kuantitatif bahwa optimasi yang dilakukan benar-benar berdampak.

4. Tantangan terbesar adalah memastikan hasil pengukuran akurat. Pada run pertama, JVM belum optimal karena JIT compiler masih dalam proses warm-up sehingga hasilnya cenderung lebih lambat dari kondisi sebenarnya. Cara mengatasinya adalah menjalankan aplikasi beberapa kali dan baru mengambil pengukuran setelah JIT compiler stabil.

5. Manfaat utamanya adalah kemampuan untuk melihat CPU time secara per-method langsung di dalam IDE yang sama tanpa perlu tools tambahan. Ini mempercepat siklus identifikasi -> analisis -> optimasi karena semuanya terintegrasi dalam satu environment.

6. Kedua tools mengukur hal yang berbeda. IntelliJ Profiler mengukur CPU time per method secara isolated, sedangkan JMeter mengukur response time end-to-end termasuk network latency, I/O, dan faktor eksternal lainnya. Jika hasilnya tidak konsisten, perlu dicek apakah ada faktor lain di luar kode seperti koneksi database atau load yang mempengaruhi hasil JMeter.

7. Strategi yang digunakan adalah mengidentifikasi bottleneck lewat profiling terlebih dahulu sebelum melakukan perubahan apapun, lalu mengoptimasi hanya pada bagian yang terbukti lambat. Untuk memastikan fungsionalitas tidak berubah, hasil response dari endpoint dicek kembali setelah refactoring untuk memverifikasi bahwa data yang dikembalikan tetap sama dan benar.