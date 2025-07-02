
# Istanbul Trafik Yoğunluğu Analizi
Bu proje, İstanbul'a ait saatlik trafik verilerini kullanarak şehir genelindeki trafik davranış kalıplarını analiz eder. Trafik yoğunluğu, hız değerleri ve coğrafi konum bilgileri temel alınarak, makine öğrenmesi tabanlı K-Means kümeleme algoritması ile trafik örüntüleri belirlenmiş, bu örüntüler hem sayısal hem de coğrafi haritalar üzerinde görselleştirilmiştir. Projenin temel amacı, farklı trafik davranışlarının otomatik olarak gruplandırılması ve bu grupların şehir haritası üzerinde gösterilerek trafik yönetimi ve şehir planlaması için anlamlı çıkarımlar yapılmasını sağlamaktır.

## 1. Veri Seti Tanıtımı
### 1.1. Veri Kaynağı
Bu projede kullanılan veri seti, T.C. Ulaştırma ve Altyapı Bakanlığı Açık Veri Portalı aracılığıyla kamuya açık şekilde sunulan "İstanbul Saatlik Trafik Yoğunluğu Verisi (Ocak 2025)" başlıklı bir veri kümesidir. Veri setine doğrudan aşağıdaki adresten erişilebilir:


https://ulasav.csb.gov.tr/dataset/34-hourly-traffic-density-data-set 

### 1.2. Veri Biçimi
Veri seti, 

.csv formatında toplam 12 farklı dosya olarak sağlanmıştır. Bu dosyalar İstanbul'un farklı bölgelerine ve zaman dilimlerine ait saatlik trafik ölçümlerini içermektedir. Tüm dosyalar projede birleştirilerek tek bir bütünsel veri çerçevesi haline getirilmiştir.


### 1.3. Veri Setindeki Temel Öznitelikler
Veri setinde aşağıdaki temel öznitelikler bulunmaktadır:


DATA TIME: Gözlemin yapıldığı tarih ve saat 


LATITUDE: Gözlem noktasının enlemi 


LONGITUDE: Gözlem noktasının boylamı 


GEOHASH: Konuma özel şifreli tanım (projede kullanılmadı) 


MINIMUM_SPEED: Saatlik periyottaki minimum hız (km/s) 


MAXIMUM_SPEED: Saatlik periyottaki maksimum hız (km/s) 


AVERAGE_SPEED: Saatlik periyottaki ortalama hız (km/s) 


NUMBER_OF_VEHICLES: Saatlik periyotta ölçülen araç sayısı 

## 2.Kurulum ve Çalıştırma

Projenin tam olarak çalışabilmesi için aşağıdaki adımları sırasıyla uygulayın:

### 2.1. Projeyi Klonlama
Öncelikle bu depoyu yerel bilgisayarınıza klonlayın:
```
git clone [https://github.com/pinarlizeynep/IstanbulTrafikAnalizi.git](https://github.com/pinarlizeynep/IstanbulTrafikAnalizi.git)
cd IstanbulTrafikAnalizi
```

### 2.2. Gerekli Kütüphaneleri Yükleme
Şu kütüphaneleri manuel olarak yükleyin:
```
pip install pandas numpy matplotlib seaborn folium
```

### 2.3. Veri Setlerini İndirme ve Yerleştirme
Bu projenin analizi için kullanılan büyük trafik yoğunluğu veri setleri GitHub'a doğrudan yüklenemeyecek kadar büyüktür. Kodun düzgün çalışabilmesi için lütfen aşağıdaki kaynaktan veri setlerini indirip, projenin kök dizininde (yani klonladığınız IstanbulTrafikAnalizi klasörünün içinde) veriler/ adında bir klasör oluşturup tüm .csv dosyalarını bu klasörün içine yerleştirin.

Veri Kaynağı:
https://ulasav.csb.gov.tr/dataset/34-hourly-traffic-density-data-set

Adımlar:

1) Yukarıdaki bağlantıya gidin.
2) Sayfada bulunan tüm traffic_density_YYYYMM.csv dosyalarını bilgisayarınıza indirin.
3) Proje klasörünüzün içinde veriler/ adında yeni bir klasör oluşturun.
4) İndirdiğiniz tüm .csv dosyalarını bu veriler/ klasörünün içine taşıyın.

### 2.4. Jupyter Notebook'u Çalıştırma
Tüm adımları tamamladıktan sonra, Jupyter Notebook'u başlatın ve analiz dosyasını açın:


```
jupyter notebook
```
Açılan web arayüzünde istanbul_trafik_analizi_1.ipynb dosyasını bulun ve tıklayarak açın. Notebook içerisindeki hücreleri sırasıyla çalıştırarak analizi görüntüleyebilir ve tekrar edebilirsiniz.

## 3. Modelleme 

Bu projede temel yöntem olarak K-Means Kümeleme algoritması kullanılmıştır. Modelleme süreci üç ana başlıkta gerçekleştirilmiştir:


1) Kümeleme Algoritmasının Uygulanması: K-Means algoritması scikit-learn kütüphanesi kullanılarak KMeans() sınıfı ile uygulanmıştır. Kümeleme işlemi, önceden normalize edilmiş 8 öznitelik üzerinde yürütülmüştür.


2) Küme Sayısı Seçimi (Optimum k Değeri): Optimum k değeri iki farklı yöntemle belirlenmiştir:


  Elbow (Dirsek) Yöntemi: Farklı k değerleri için modelin inertia değeri hesaplanmış ve grafiği çizilmiştir. Grafik üzerindeki "dirsek" noktası gözlemlenerek 

  k=4 değeri optimum olarak seçilmiştir.
  ![image](https://github.com/user-attachments/assets/0812a631-7575-4953-993e-b1fb5affd66d)



  Silhouette Skoru: Her bir veri noktasının kendi kümesine olan yakınlığı ile en yakın diğer kümeye olan uzaklığı karşılaştırılmış, en yüksek skorun k=4 için elde   edildiği gözlemlenmiştir.

  Bu iki yöntemin birlikte değerlendirilmesi sonucu modelde 4 küme kullanılması kararlaştırılmıştır.


3) Modelin Açıklanabilirliği ve PCA Yöntemi: Küme etiketleri veri çerçevesine eklenerek her satırın hangi trafik davranış kümesine ait olduğu yorumlanabilir hale getirilmiştir. Küme merkezlerinin yorumlanabilirliğini artırmak amacıyla veri boyutu PCA (Principal Component Analysis) yöntemiyle 2 boyuta indirgenmiş ve kümeler matplotlib ile görselleştirilmiştir.
   ![image](https://github.com/user-attachments/assets/0fc93d9b-dc98-4436-878d-6d0572ca422a)

## 4. Sonuçlar ve Yorum 

K-Means algoritması ile elde edilen dört trafik kümesi, hem sayısal analizle hem de coğrafi harita görselleştirmesiyle detaylı şekilde incelenmiştir. Harita görselleştirmesi, Python'da Folium kütüphanesi kullanılarak oluşturulmuştur.
![image](https://github.com/user-attachments/assets/241f30d3-ecb4-472f-916b-ff7186e48478)

### 4.1. Kümelerin Anlamlandırılması 
Kırmızı Noktalar: Ortalama yoğunluk (araç sayısı) yüksek, ortalama hız düşük-orta seviyesindedir. Yoğun ve sabit trafik gözlemlenir. Ağırlıklı olarak Avrupa Yakası'nın merkezi bölgelerinde (Beşiktaş, Fatih, Bakırköy, Şişli gibi) yoğunlaşmıştır, ana arterlerde ve iş merkezlerinde görülmektedir.

Yeşil Noktalar: Ortalama yoğunluk (araç sayısı) düşük, ortalama hız ise yüksektir. Yollar akıcı ve boştur. Özellikle TEM otoyolu, Kuzey Marmara Otoyolu ve şehir çevresi boyunca yayılmıştır.

Mavi Noktalar: Ortalama yoğunluk (araç sayısı) değişkendir. Ortalama hız zaman zaman yüksek zaman zaman düşüktür. Kararlı bir trafik yapısı görülmez ve İstanbul'un hem Avrupa hem de Asya yakasında iç bölgelere dağılmıştır.

Turuncu Noktalar: Ortalama yoğunluk (araç sayısı) ve ortalama hız orta seviyededir. Mavi ve kırmızı arasında geçiş niteliğinde bölgeleri temsil etmektedir; trafik orta yoğunlukta ve zamansal değişkenliğe açıktır.

### 4.2. Trafik Davranış Kalıplarına Yönelik Yorumlar 

Bölgesel Trafik Dağılımı: Avrupa Yakası, Asya Yakası'na göre çok daha fazla yüksek yoğunluklu (kırmızı) kümelenme göstermektedir.


Çevresel Yolların İşlevi: Çevreyolları (özellikle yeşil noktalı bölgeler), şehir içi trafiğin yoğunluğunu azaltan bölgeler olarak belirginleşmiştir.

Trafik Örüntülerinin Mekansal İlişkisi: Harita üzerinde gözlemlenen kümeler, sadece hız değil, aynı zamanda konumsal bağlamda da anlamlı bir dağılım göstermektedir. Bu durum, modelin coğrafi bağlamda doğru genellemeler yapabildiğini göstermektedir.

### 5.3. Genel Değerlendirme 
Model sonucunda ortaya çıkan kümeler, İstanbul'un trafik yapısını bölgesel ve zamansal olarak başarılı şekilde temsil etmektedir. Özellikle yüksek yoğunluklu ve sabit trafikli bölgelerin net şekilde ayrılması, şehir planlaması açısından değerli bilgiler sunmaktadır. Elde edilen bu sınıflandırma; ulaşım altyapısı karar destek sistemlerinde kullanılabilir, trafik yönetim merkezleri tarafından gerçek zamanlı yönlendirme için geliştirilebilir ve otonom sürüş sistemleri veya navigasyon uygulamaları için veri temelli karar mekanizması sağlayabilir.



