<img width="1500" height="500" alt="image" src="https://github.com/user-attachments/assets/8070369d-be9d-4436-bb40-4c342d056fa2" />


# Technocore DID Rehberi: AI Ajanlarına Kimlik Kazandırmak

## Technocore Nedir?

Technocore, AI ajanlarının (ve bu ajanları çalıştıran insanların) birbirleriyle mesajlaşabildiği küçük bir HTTP tabanlı sistemdir. Sistemin temel fikri basit: her katılımcı, kendine ait şifreli bir dijital kimlik (DID) oluşturur, bu kimlikle mesajlarını imzalar ve Technocore üzerindeki "oda"lara (room) bu imzalı mesajları gönderir. Böylece kim ne söylemiş, kimin imzasıyla söylemiş, dışarıdan doğrulanabilir hale gelir.

Bu yazıda,Technocore'a nasıl katılabileceğinizi, bir DID'in ne işe yaradığını ve süreci komut satırından adım adım nasıl uygulayabileceğinizi anlatıyorum. Anlattığım her adımı kendi sunucumda birebir uyguladım; ekran çıktıları da gerçek.

## DID Nedir, Neden Önemli?

DID (Decentralized Identifier), merkezi bir otoriteye (bir şirkete, bir sunucuya) bağlı olmadan sahip olduğunuz bir kimlik türüdür. Technocore'da kullanılan DID'ler `did:key:z6Mk...` formatında olup Ed25519 adlı bir kriptografik imza algoritmasına dayanır.

Pratikte bu şu anlama gelir:
- Kimliğinizi siz oluşturursunuz, kimse size vermez.
- Özel anahtarınız (private key) yalnızca sizin bilgisayarınızda/sunucunuzda, şifreli halde durur.
- Gönderdiğiniz her mesaj bu özel anahtarla imzalanır; herkes bu imzayı, sizin public anahtarınızı (DID'inizi) kullanarak doğrulayabilir.
- Yani "bu mesajı gerçekten bu kimlik gönderdi mi?" sorusunun cevabı matematiksel olarak kanıtlanabilir, bir sunucuya güvenmeniz gerekmez.

Validator dünyasından gelenler için bu kavram hiç yabancı değil: orada da her blok/işlem bir imza ile doğrulanır, merkezi bir otoriteye güvenmezsiniz. Technocore aynı mantığı ajan ve içerik üreticisi iletişimine taşıyor.

## Adım Adım Kurulum

Aşağıdaki adımları bir Linux sunucusunda (Ubuntu tabanlı) uyguladım; macOS ve Windows için de küçük farklarla aynı mantık geçerli.

### 1. Python ve Git'i doğrulayın

```bash
python3 --version
git --version
```

İkisi de kurulu değilse:

```bash
sudo apt update
sudo apt install -y python3 python3-venv python3-pip git
```

### 2. Repoyu klonlayın ve sanal ortam kurun

```bash
git clone https://github.com/zunmax/technocore-did-starter.git
cd technocore-did-starter
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

Bu adım, imzalama işlemleri için gerekli olan `cryptography` kütüphanesini izole bir ortama kurar; sisteminizin geri kalanını etkilemez.

### 3. Kurulumu doğrulayın

```bash
python --version
python -c "import cryptography; print(cryptography.__version__)"
python technocore_agent.py --version
```

Beklenen çıktı Python 3.12.x, `cryptography` için 50.0.0 (Windows/Linux/Apple silicon) veya 48.0.1 (Intel Mac), ve tool versiyonu 1.0.0'dır.
<img width="988" height="150" alt="image" src="https://github.com/user-attachments/assets/2ee9e4ac-8874-459d-b19e-b57ffc3b7afc" />


## Kimliğinizi Oluşturun

Bu adım **yalnızca bir kez** çalıştırılır:

```bash
python technocore_agent.py init
```

Sizden en az 12 karakterlik bir passphrase istenir, iki kez girilir. Komut, yerelde şifreli bir `identity.pem` dosyası oluşturur ve ekrana şöyle bir çıktı verir:

did:key:z6Mk...sizin-benzersiz-public-anahtarınız...

# Benim kullandığım Agent DID: did:key:z6MkwF7M9vs5Z98XV4kBUBcYEaR9zSrXMkJ2mKAdJgwmiaaF

Bu satırı (sadece bu satırı, passphrase'i değil) kaydedin ve paylaşabilirsiniz. Passphrase'inizi ise ayrı, güvenli bir yerde saklayın; merkezi bir kurtarma sistemi yoktur, kaybederseniz kimliğinize erişemezsiniz.

<img width="1408" height="152" alt="image" src="https://github.com/user-attachments/assets/74008c38-7647-481c-b3e6-a8a8f01eb767" />


Dosya iznini de kısıtlamanızı öneririm:

```bash
chmod 600 identity.pem
```

Böylece aynı sunucudaki başka kullanıcılar/işlemler bu dosyayı okuyamaz.

**Güvenlik notu:** `identity.pem` dosyanızı asla bir Git deposuna eklemeyin, paylaşmayın veya ekran görüntüsünde göstermeyin. Paylaşacağınız tek şey `did:key:...` ile başlayan public kısımdır.

## Technocore'a Katılın

Oluşturduğunuz kimlikle lobiye imzalı bir tanışma mesajı gönderin:

```bash
python technocore_agent.py say lobby "Merhaba Technocore. ZeycaNode olarak validator işletiyorum, buraya Türkçe konuşan topluluk için bir DID/imzalama rehberi getirdim."
```

Passphrase'inizi tekrar girmeniz istenir. Komut başarılı olursa, size şuna benzer bir JSON döner:

```json
{
  "posted": {
    "seq": 6675,
    "ts": "2026-08-24T18:08:43.920614Z",
    "from": "did:key:z6Mk...",
    "text": "Merhaba Technocore. ZeycaNode olarak...",
    "nonce": 1787594911412676596
  }
}
```


`seq` numarası, mesajınızın sistemdeki sırasını gösterir. Bunu katılımınızın kanıtı olarak saklayabilirsiniz.

**Pratik bir not:** Technocore'un sunucusu zaman zaman geçici olarak yanıt vermeyebilir (502 Bad Gateway gibi hatalar alabilirsiniz). Bu durumda birkaç dakika bekleyip aynı komutu tekrar denemek genellikle yeterlidir; sorun sizin kurulumunuzda değil, servis tarafındadır.

## Bir Katkı Üretin ve Kaydedin

Technocore'un vurguladığı nokta şu: katkı, kod yazmak zorunda değil. Bir X thread'i, bir video, bir makale, bir çeviri veya küçük bir araç, hepsi geçerli. Önemli olan, Technocore'u ve DID mantığını başka insanların anlamasına gerçekten yardımcı olmak.

Katkınızı yayınladıktan sonra, linkini aynı kimlikle Technocore'a kaydedersiniz:

```bash
python technocore_agent.py say technocore "I published a Technocore contribution: <İÇERİK_LİNKİNİZ>. It helps people understand <ne anlattığınız>."
```

Dönen cevaptaki `room`, `seq` ve `from` (DID'iniz) bilgilerini not alın. Bunlar, içeriğinizle imzalı kaydınız arasındaki bağlantıyı oluşturur.
<img width="1938" height="412" alt="image" src="https://github.com/user-attachments/assets/bf56e49a-af86-4ca2-a39b-753ecaaacb5f" />


## Sonuç

Bu rehberde anlattığım her adımı kendi sunucumda uyguladım: kurulumdan kimlik oluşturmaya, lobiye katılım mesajı göndermeye kadar. Amacım, Türkçe konuşan blockchain ve validator topluluğuna, Technocore'un ne olduğunu ve DID tabanlı imzalama sisteminin nasıl çalıştığını anlaşılır, uygulanabilir bir şekilde aktarmaktı.

Unutulmaması gereken önemli bir nokta: bu süreci tamamlamak herhangi bir ödül garantisi vermez; Flop Labs'ın ileride açıklayacağı kurallara tabidir. Buradaki asıl değer, sürecin kendisini anlamak ve Türkçe kaynak eksikliğini bir nebze kapatmaktır.

---

*Sorularınız için: Twitter [@DigitalMinion2](https://twitter.com/DigitalMinion2) | Discord: zcg3228*
