
Bu satırı (sadece bu satırı, passphrase'i değil) kaydedin ve paylaşabilirsiniz. Passphrase'inizi ise ayrı, güvenli bir yerde saklayın; merkezi bir kurtarma sistemi yoktur, kaybederseniz kimliğinize erişemezsiniz.

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

## Sonuç

Bu rehberde anlattığım her adımı kendi sunucumda uyguladım: kurulumdan kimlik oluşturmaya, lobiye katılım mesajı göndermeye kadar. Amacım, Türkçe konuşan blockchain ve validator topluluğuna, Technocore'un ne olduğunu ve DID tabanlı imzalama sisteminin nasıl çalıştığını anlaşılır, uygulanabilir bir şekilde aktarmaktı.

Unutulmaması gereken önemli bir nokta: bu süreci tamamlamak herhangi bir ödül garantisi vermez; Flop Labs'ın ileride açıklayacağı kurallara tabidir. Buradaki asıl değer, sürecin kendisini anlamak ve Türkçe kaynak eksikliğini bir nebze kapatmaktır.

---

*Sorularınız için: Twitter [@DigitalMinion2](https://twitter.com/DigitalMinion2) | Discord: zcg3228*
