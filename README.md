#LOL RANKED GAMES PROJECT REPORT
Melis Bayındır
TECHCAREER.NET DATA SCIENCE BOOTCAMP GRADUATION PROJECT
Giriş
Date Set : '(LoL) League of Legends Ranked Games'
Neden Bu Veri Setini Seçtim?
Bu projede veri setini seçerken ilgi alanlarıma göre bir veri seti seçmek için oyunlar ile
ilgili veri setlerini araştırdım. İstediğim aslında birden fazla oyunun çeşitli featurelarına göre
oyun başarısını modelleyebileceğim bir data set bulmaktı fakat buna yönelik bir şey
bulamadım. Bu yüzden de oyunlar ile ilgili en kullanılabilir veri setlerine yöneldim.
Kendimin de yeni yeni oynamaya başladığı League of Legends oyunu ile ilgili bu veri
setine denk geldim. Oyunu oynarken aklıma takılan oyun öncesinde veya sırasındaki
eylemlerin oyun sonucuna etkisi ile ilgili sorulara cevap bulabilmenin çalışmama ilgi çekici
bir etmen katacağına inanarak, bu veri seti ile ilerlemeye karar verdim.
Bu çalışmamda '(LoL) League of Legends Ranked Games' datasını inceleyerek çeşitli
çıkarımlar, en sonunda da bu çıkarımlarımı inceleyerek oyun sonucunda kazanan takımı
tahminleyecek 2 farklı model geliştirmek istedim.
 İlk model ile datanın kendisinde bulunan ve oyunun başından son kısmına kadar olan
bilgileri içeren sütunları modellememde kullanarak, oyun sürecindeki verilere göre
hangi takımın kazandığını bilgisayara buldurtmayı amaçladım.
 İkinci modelimde ise, daha erken oyunda, sadece oyun öncesi kararlaştırılan şeyler ve
oyuna ilk girildiğinde yaklaşık 5 dakika içerisinde elde edilecek bilgiler ile
bilgisayarın oyun sonucunu tahmin etmesini sağlamak istedim. Beklentilerimi
karşılayarak, ilk modelimde elde ettiğim doğruluk oranı ikinci modelime göre oldukça
yüksekti.
Data Seti Konusu: LOL Ranked Oyunlarına Genel Bakış:
"League of Legends," oyuncuların farklı şampiyonları kontrol ederek, büyüler
kullanarak rakip oyuncularla savaşıp, kuleleri savunarak ve orman canavarlarını avlayarak
deneyim kazandığı çok oyunculu çevrimiçi savaş oyunudur. Oyunda şampiyonlar özel
yeteneklere sahip olup, kuleler ana üssü savunmak için kullanılırken, ejderha ve Baron
Nashor gibi büyük canavarlar, takımlar arasında büyük bir rekabetin yaşandığı özel güçler ve
avantajlar sağlar. Oyuncular, stratejik yeteneklerini kullanarak takım çalışmasıyla birlikte
oyunun ana unsurlarını kullanarak zafer elde etmeye çalışırlar.
Data’da yer alan veriler:
 -2 takım ve her bir takımda bulunan 5 şampiyon, ve her bir şampiyonun oyun
başlamadan önce aldığı 2’şer büyü (t(i)_cham(j)id, t(i)_champ(j)_sum(k)
 -İlk orman canavarlarını hangi takım aldı? firstBaron firstDragon firstRiftHerald
 -İlk kuleleri , ilk kill’i, ilk inhibitörleri hangi takım aldı?
 -Takımlara ait toplam alınan orman canavarları sayısı, kule ve inhibitör sayısı
 -oyunun oynandığı tarih ve zaman
 -oyunun total süresi ve kazananıMelis Bayındır
Datayı İnceleme
Csv bilgisayar kaydı olduğu için oldukça temiz bir şekilde kaydedilmişti. Değerler sadece
int olarak bulunuyordu ve de null değer yoktu. 51490 satır ve 61 sütun ile oldukça büyük bir
veri seti olduğu söylenebilir. Gerekli olmadığını düşündüğüm sütunları silerek incelememe
başladım. İleride incelememi kolaylaştırması için json dosyasında id’lere göre kaydedilen
şampiyon ve büyü isimlerini dataya ekledim, epoch time formatına date-time formatına
çevirerek ekstra bir sütun olarak her bir oyuna haftanın hangi günü oynandığı bilgisini
ekledim. İşime yaramayacağını düşündüğüm sütunları düşürerek devam ettim. İlk aşamada
yaptığım bu incelemeler ve düzenlemeler sonucunda, içgörü kazanabilmek için görselleştirme
kısmı ile devam ettim.
Görselleştirme
Bu kısımda ilk yapmak istediğim şey oyunların hangi tarihlerde ne sıklıkla oynandığına
bakmaktı. Bunun için oluşturduğum grafikten ise beklemediğim bir dağılım gözlemledim.
(Yaptığım işlemin doğruluğu konusunda emin değilim)
İlerleyen aylarda oyun sayısı giderek artıyor gibi gözüküyor. Bu tablo iki şeyi
düşünmeme sebep oldu. iki sonuç çıkabileceğini düşünüyorum:
 sonuç--> oyunun ilerleyen zamanlarında oyun popülerliğinin ciddi ölçüde
artmış olması sonucu oynanma miktarlarının artması.
 sonuç --> oluşturulan csv, oynanan oyunların datalarını yeni zamanlıları daha
çok göstermek üzere ayarlanmış, veya zamanlama olarak arada missing values
bulunmakta.
İncelediğim bir diğer sonuç da aynı tarih ve saatte(hatta aynı saniyede) üretilmiş
oyunların varlığı oldu.Melis Bayındır
Incelemelerime haftanın hangi günlerinde daha sık oyuna girildiğini gözlemleyerek
devam ettim. Pazar günü max sayıda oyun oynanmış olması beni şaşırtmasa da, Salı
günü oyunlarının Cuma gününden fazla olması ilginçti. Modellememde, haftanın
günlerinin de etki edeceğini düşünüyorum.
Oyun sürelerinin dağılımını, modellememde oldukça etkili görüyorum. Daha oyun
başlar başlamaz AFK kalan oyuncuların sonucunda hemen teslim olunduğu veriler,
genel olarak modeli negatif etkileyebilir. Bu sebeple de oyunda ilk 15 dk öncesinde
teslim olma gerçekleştiyse, bu verileri düşürüyorum.Melis Bayındır
Sonraki adımımda da büyü dağılımlarını inceledim, ve en çok tercih edilen büyünün
neredeyse yarı yarıya bir oranla Flash olduğunu gözlemledim.
Yine en çok tercih edilen ve banlanan karakterlerin grafiklerini merak ettim ve bunları
bar plot şeklinde oluşturdum.Melis Bayındır
Görselleştirmemin son kısmında ise en çok kazanma oranına sahip 20 karakteri
gözlemledim.
Sorular Sorma
Aslında ilk olarak görselleştirmeden önceki incelemelerimde sonuca etki edeceğini
düşündüğüm incelemeleri yapmıştım. Sonrasında da bu incelemelerde zorlanma durumuma
göre soruları incelediğim şeylere göre ayırdım.
İlgimi çeken sonuçlar, aynı zamanda da beklenebilecek sonuçlar vardı. Mesela ilk
kuleyi alan takım, yaklaşık 2.5 kat ile daha fazla oyun kazanmıştı. Benzer şekilde inhibitörler,
ilk kill, orman canavarları için de farklı oranlarda da olsa kazandırdığı avantajlar gözle
görülür bir şekilde fark ediyordu.
 Oyunda her takıma ait 2 inhibitör var, ve yıkıldıklarında belli bir süre sonra
yenileniyorlar. 50k oyun arasında bir takım en çok 11 inhibitör kesebilmiş, ve
bu oyun beklenebileceği gibi oldukça uzun, 64 dakika sürmüş.
 En uzun süren oyun yaklaşık 80 dk sürmüş ve totalde 18 kule parçalanmış.
 Kulelere vuruş gücünü arttıran orman canavarı Rift Herald, sadece oyunun 20.
Dakikasına kadar vardır. Oynanan oyunların yaklaşık yarısında, hiç bir takım
bu canavarı öldürmemiş. Öldüren takımlar da neredeyse üç kat oranla daha
fazla sayıda ilk kuleyi alabilmişler ve neredeyse 2 kat oranla da daha fazla maç
kazanmışlar.
 En çok tercih edilen veya banlanan karakterlerin takımlarda yer almasının maç
sonuçlarına çok büyük bir etkisi olmadığını gözlemledim.
İncelediğim ilginç bir veri de, büyülerden özel bir büyü olan smite ile ilgili oldu.
Smite(çarp), alışılan LOL mantığında, her bir takım içerisinde orman canavarları ile
savaşacak karakterin alması gereken bir büyü. Gözlemlediğim kadarı ile de oyunların
%98’inde bu mantığa uyulmuş. Fakat geri kalan yaklaşık 1000 oyunda, her iki takımdan da hiçMelis Bayındır
çarp almayan sadece iki oyun bulunurken, oyundaki 10 kişiden totalde 3,4 ve 6 kişinin de
aldığı durumlar var.
Benim merak ettiğim kısım ise daha çok takımlardan sadece birinde çarp bulunan bir
karakterin olmasının, kazanma oranlarını nasıl değiştirdiği oldu.
Çarp kullanımının hiç kullanılmamasına göre kazanma ihtimalini yaklaşık 1.5 katına
çıkardığını gözlemledim.
Modelleme
En başta da dediğim gibi, 2 türlü modelleme yapmak istedim.Her iki modelimde de
tree regression kullanmayı tercih ettim.
İlkinde bilgisayarın sonucun ne olduğunu tüm maç süresince elde edilen verilere
dayanarak bulmasını istedim. Sonucunda yaklaşık 0.97 oranında bir accuracy elde
ettim. Beklediğim gibi oldukça yüksek gelen bir sonuç oldu. Kullandığım input verileri,
oyunun oldukça sonlarına ait bilgileri içeriyordu.
2. modellemem ise sadece oyun öncesinde seçilen ve banlanan karakterler, seçilen
büyüler, ilk kanı ve ilk kuleyi kimin aldığı, hangi gün oyunun oynandığı verileri
içeriyordu. Bu modellememin sonucunda ise 0.62 oranında bir accuracy elde ettim.
Her iki modelin de beklentilerimi karşıladığını söyleyebilirim. 2. Modelin daha da gelişmeye
açık olduğunu biliyorum, fakat oyunun ilk 5 dakikasında sonucunu az çok tahminleyebilmesi
için %62’nın başlangıç olarak iyi bir değer olduğunu düşünüyorum.
Sonuç
Hem veri setini incelerken, hem modellerken, oynamaktan zevk aldığım bir oyunun
işleyişini daha yakından inceleyebilmek oldukça eğlenceliydi. Zaman zaman istediğim
sonucu nasıl elde edeceğimi bulamayıp zorlandığım anlar olsa da, tüm sürecin
oldukça eğitici olduğunu ve teorik olarak öğrendiğim birçok bilgiyi pekiştirmek için
etkili olduğunu düşünüyorum. Elde ettiğim sonuçlar hem beklentilerimi karşıladı, hem
de şaşırttı. Özellikle ikinci modelimin üzerinde daha fazla fikir yürütebilmek, ve
accuracy değerini daha da düzgün bir şekilde arttırabilmek istiyorum.
