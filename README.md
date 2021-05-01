API: https://openweathermap.org/api

Bu adresteki **sign up** linkine tıklayarak üye olun.

Üye olduktan sonra e-mail adresinize gelen onaylama butonuna tıklayın.

Ardından **https://home.openweathermap.org/api_keys** adresine giderek API Key'inizi kopyalayın ve unutmayacağınız bir yere kaydedin. Bu Key sayesinde bu ücretsiz API üzerinden hava durumu verilerini çekebileceksiniz.

Projenizin için bir klasör oluşturun ve bu klasör içinde React projenizi oluşturun.

`npx create-react-app .`

Sondaki nokta bulunduğunuz klasör içinde projenizi kurmak için gereklidir. Bu komutu kullandığınız klasör boş olmalıdır.

Projenizin ana dizininde `.env` isimli bir dosya oluşturun ve Key'inizi bu dosya içinde `REACT_APP_WEATHER_API_KEY` olarak kaydedin.

`REACT_APP_WEATHER_API_KEY=api_keyiniz_buraya`

Create-React-App ile oluşturulan React projelerinde environment variable kullandığınızda `REACT_APP_` ön ekiyle başlatmanız gerekir.

Oluşturduğunuz `.env` dosyasını `.gitignore` dosyanıza eklemeyi unutmayın.

Şimdi `src` klasörü içindeki bütün dosyaları silin ve iki adet dosya oluşturun: `App.js` ve `index.js`.

`App.js`

```javascript
const App = () => {
  return (
    <div>
      <h2>Hava Durumu</h2>
    </div>
  );
};

export default App;
```

`index.js`

```javascript
import { render } from "react-dom";
import App from "./App";

const root = document.querySelector("#root");

render(<App />, root);
```

Uygulamamızı en basit haliyle çalışır hale getirdik. Şimdi terminalden uygulamamızı başlatalım.

`npm start`

Şimdi kullanacağımız API Endpoint'i bulalım.

**https://openweathermap.org/current** adresine gidin ve **By geographic coordinates** bölümündeki örnek linki kopyalayın.

```
https://api.openweathermap.org/data/2.5/weather?lat={lat}&lon={lon}&appid={API key}
```

Bu link içine üç tane parametre gireceğiz: Enlem (lat), Boylam (lon), ve API Key.

Önce API isteğini yapacak olan paketimizi yükleyelim.

```
npm install axios
```

Aldığınız değeri tutmak için `App` fonksiyonu içinde bir state parçası oluşturalım.

```js
const [weather, setWeather] = useState();
```

Şimdi debu API isteğini yapacak `getWeatherData` isimli bir fonksiyon oluşturalım. Bu fonksiyonumuz `lat` ve `lon` değerlerini parametre olarak alacak ve `API Key` değerini Environment Variables olarak belirlediğimiz değerden alacak. Veriyi başarılı bir şekilde aldığında ise az önce oluşturduğumuz state parçası içine kaydedecek.

```js
const getWeatherData = async (lat, lon) => {
  const apiKey = process.env.REACT_APP_WEATHER_API_KEY;

  try {
    const { data } = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&appid=${apiKey}`
    );
    setWeather(data);
  } catch {
    alert("Hava durumu verileri alınamadı.");
  }
};
```

Şimdi kullanıcının giriş yaptığı IP adresine göre enlem ve boylam değerlerini alalım.

Bunun için JavaScript'in GeoLocation özelliğinden faydalanmamız gerekir. Bu işlem için kullanıcının bizim uygulamamıza lokasyon bilgilerine erişmek için izin vermesi gerekecek. Siz bu bilgiye erişmek isteyen bir kod yazdığınızda tarayıcı bunu kullanıcıya otomatik olarak soracaktır. Biz de bu izin geldikten sonra bu bilgileri kullanarak hava durumu verilerini alma işlemini gerçekleştireceğiz.

Normalde bu enlem ve boylam değerlerine ulaşmak için kodumuzu sıfırdan JavaScript ile yazabiliriz. Ancak React ile proje geliştirirken bu tür native JavaScript özelliklerinin hazır bir paket haline getirilmiş versiyonlarını kullanırsanız uygulama geliştirme hızınız artacaktır. Eğer bu işlemin sıfırdan nasıl yapıldığını öğrenmek istiyorsanız, [**şu adresteki**](https://itnext.io/creating-react-useposition-hook-for-getting-browsers-geolocation-2f27fc1d96de) makaleyi inceleyebilirsiniz.

Bu makalede GeoLocation API basit bir hook haline getirilmiştir. Siz bunu isterseniz kendinize bir utility function olarak da hazırlayabilirsiniz.

Şimdi bu paketi yükleyelim ve kullanıcımızın lokasyon bilgilerine ulaşmaya çalışalım.

```
npm i use-position
```

`App.js`

```js
const { latitude, longitude } = useLocation();

console.log({ latitude, longitude });
```

Bu kodu kaydedip uygulamanızın olduğu pencereyi yenilediğinizde tarayıcının size "Bu site lokasyon bilgilerinize erişmek istiyor" gibi bir mesajla karşılaşacaksınız. Kullanıcı bu izni vermediği sürece `latitude` ve `longitude` değerleri `undefined` olarak konsolda görünecektir. Biz de bu değerler `undefined` olduğu sürece API isteğimizi yapmayacağız ve kullanıcımıza bu isteğe olumlu cevap vermesi yönünde bir mesaj göstereceğiz.

```js
{
  latitude && longitude ? (
    "hava durumu verileri buraya"
  ) : (
    <>
      Hava Durumu verilerine ulaşabilmek için lokasyon bilgilerinize erişim izni
      vermeniz gerekir.
    </>
  );
}
```

Basit bir conditional rendering yaparak bu değerlerden birisi falsy olduğu sürece ekranda bir uyarı mesajı gösterdik ve bu değerlerin var olduğu durumda ise ekrana `hava durumu verileri buraya` şeklinde bir metin yazdırdık. Şimdi de bu verilerin geleceği kısıma gelecek bileşenimizi hazırlayalım.

`src` klasörü altında `components` isimli bir klasör oluşturalım ve bu klasörün içinde `HavaDurumu.js` ismiyle bir bileşen oluşturalım.

`HavaDurumu.js`

```js
const HavaDurumu = (props) => {
  return "Hava Durumu Verileri Buraya";
};
```

Şimdi `App.js` içindeki gerekli yere bu bileşenimizi yerleştirelim.

```js
{
  latitude && longitude ? (
    <HavaDurumu data={weather} />
  ) : (
    <>
      Hava Durumu verilerine ulaşabilmek için lokasyon bilgilerinize erişim izni
      vermeniz gerekir.
    </>
  );
}
```

Bu aşamada kullanıcı izin verdiğinde ve API'dan veriler geldiğinde bu veriler `HavaDurumu` bileşenine prop'lar aracılığıyla iletilecektir.

Şimdi `HavaDurumu` bileşenimizde bu verileri kullanmadan önce, `App.js` içerisinde `useEffect` kullanarak `latitude` ve `longitude` değerleri truthy olduğunda API isteğimizi gerçekleştirelim.

```js
useEffect(() => {
  latitude && longitude && getWeatherData(latitude, longitude);
}, [latitude, longitude]);
```

Artık kullanıcımız izin verdiği anda API isteğimiz gerçekleşecek ve `HavaDurumu` bileşenimize hava durumu verileri prop'lar aracılığıyla gönderilecektir. Biz de bu bileşenimizin içinde `props.weather` değerinden bu verilere ulaşabileceğiz.

Bu veriyi konsola yazdırdığınızda şuna benzer bir veriyle karşılaşacaksınız.

```js
{
    "coord": {
        "lon": -118.4268,
        "lat": 34.0132
    },
    "weather": [
        {
            "id": 800,
            "main": "Clear",
            "description": "clear sky",
            "icon": "01n"
        }
    ],
    "base": "stations",
    "main": {
        "temp": 289.24,
        "feels_like": 289.05,
        "temp_min": 288.71,
        "temp_max": 291.15,
        "pressure": 1016,
        "humidity": 82
    },
    "visibility": 10000,
    "wind": {
        "speed": 1.54,
        "deg": 190
    },
    "clouds": {
        "all": 1
    },
    "dt": 1619846583,
    "sys": {
        "type": 1,
        "id": 5872,
        "country": "US",
        "sunrise": 1619787904,
        "sunset": 1619836589
    },
    "timezone": -25200,
    "id": 5341114,
    "name": "Culver City",
    "cod": 200
}
```

Verilerin detaylı açıklaması için [şu adresteki](https://openweathermap.org/current) **Fields in API response** başlığına göz atabilirsiniz.

Şu an için bizim ihtiyacımız olan değerler şunlar:

```js
weather; // Hava durumu (dizi) { id, main, description, icon }
main.temp; // Sıcaklık değeri
dt; // Saniye cinsinden isteğin yapıldığı tarih
name; // Şehir/lokasyon ismi
```

Şimdi `HavaDurumu` bileşeni içerisinde bu değerleri ekrana yazdıralım.

```js
const HavaDurumu = (props) => {
  const { weather } = props;

  return (
    <div>
      <h3>{weather.name}</h3>
      <h5>{weather.weather.map((el) => el.description).join(", ")}</h5>
      <p>Sıcaklık: {weather.main.temp} ℃</p>
      <p>Tarih: {new Date(weather.dt * 1000).toLocaleDateString()}</p>
    </div>
  );
};
```

`HavaDurumu` bileşenini bu şekilde kaydettiğimizde hata alacağız:

```
TypeError: Cannot read property 'name' of undefined
```

Bunun sebebi, uygulamamız ilk yüklendiğinde bu verilerin henüz API'dan alınmamış olması. Bu yüzden, buradaki `return` ifadesini, prop'dan gelen `weather` değeri truthy olması şartıyla göstereceğiz. Aksi takdirde verilerin yüklendiğine dair ya da henüz verinin alınmadığına dair dilediğiniz gibi bir şey gösterebilirsiniz. Ben `Yükleniyor...` şeklinde bir metin göstermek istiyorum.

```js
const HavaDurumu = (props) => {
  const { weather } = props;

  if (weather) {
    return (
      <div>
        <h3>{weather.name}</h3>
        <h5>{weather.weather.map((el) => el.description).join(", ")}</h5>
        <p>Sıcaklık: {weather.main.temp} ℃</p>
        <p>Tarih: {new Date(weather.dt * 1000).toLocaleDateString()}</p>
      </div>
    );
  }

  return "Yükleniyor...";
};
```

Bu noktada yaptığımız API isteğinde düzeltmemiz gereken iki yer var:

Birincisi, uygulamamızın dili varsayılan olarak ingilizce çünkü biz API isteğini yaparken herhangi bir dil seçeneği belirtmedik. Kullanıcının kullandığı tarayıcıdan dilini öğrenip bunu API isteğimize eklememiz gerekecek.

İkincisi, sıcaklık değeri varsayılan olarak Kelvin birimine göre ayarlanmış. Bizim bunu metrik sistemdeki birime, yani Celsius'a çevirmemiz gerekecek.

Kullanıcının dil seçeneğini tarayıcıdan okumak için `getWeatherData` fonksiyonu içinde şu değişiklikleri yapalım:

```js
const getWeatherData = async (lat, lon) => {
  const apiKey = process.env.REACT_APP_WEATHER_API_KEY;
  const lang = navigator.language.split("-")[0];

  try {
    const { data } = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&appid=${apiKey}&lang=${lang}&units=metric`
    );
    setWeather(data);
  } catch {
    alert("Hava durumu verileri alınamadı.");
  }
};
```

`lang` değerini kullanıcının tarayıcısından `navigator.language` değeriyle okuduk. Bu değer bize `en-EN` şeklinde bir değer verdi. Bize bu değerin tire (-) işaretine kadar olan kısmı lazımdı. Bu yüzden bu string üzerinde `split` fonksiyonunu kullandık ve tire olan yerlerden metni böldük. Gelen diziden de ilk eleman olan sıfırıncı elemanı `lang` değerine atadık.

Bir de `axios.get` metodu içerisine girdiğimiz linke `units=metric` değerini ekledik.

Uygulamamızı şimdi yenilediğimizde dilin tarayıcınızın diliyle aynı olduğunu ve sıcaklık değerinin düzeldiğini göreceksiniz.

`HavaDurumu` bileşeni üzerinde istediğiniz biçimlendirmeyi yapabilir, gelen veri içinden başka değerleri de okuyarak bu bileşeni daha güzel bir hale getirebilirsiniz.
