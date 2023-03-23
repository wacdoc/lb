# Baut Ären eegene SMTP Mail Sender Server

## Préambel

SMTP kann direkt Servicer vu Cloud Ubidder kafen, sou wéi:

* [Amazon SES SMTP](https://docs.aws.amazon.com/ses/latest/dg/send-email-smtp.html)
* [Ali Cloud E-Mail Push](https://www.alibabacloud.com/help/directmail/latest/three-mail-sending-methods)

Dir kënnt och Ären eegene Mail Server bauen - onlimitéiert Sendung, niddreg Gesamtkäschten.

Drënner weisen mir Schrëtt fir Schrëtt wéi een eisen eegene Mailserver bauen.

## Server Auswiel

De selbstgehostte SMTP Server erfuerdert eng ëffentlech IP mat Ports 25, 456, an 587 op.

Allgemeng benotzt ëffentlech Wolleken hunn dës Ports als Standard blockéiert, an et kann méiglech sinn se opzemaachen andeems Dir eng Aarbechtsbestellung ausginn, awer et ass schliisslech ganz lästeg.

Ech recommandéieren vun engem Host ze kafen, deen dës Ports op huet an ënnerstëtzt d'Opstelle vun ëmgedréint Domainnamen.

Hei recommandéieren ech [Contabo](https://contabo.com) .

Contabo ass en Hosting Provider baséiert zu München, Däitschland, gegrënnt an 2003 mat ganz kompetitiv Präisser.

Wann Dir Euro als Akafswährung wielt, gëtt de Präis méi bëlleg (e Server mat 8GB Erënnerung a 4 CPUs kascht ongeféier 529 Yuan pro Joer, an déi initial Installatiounsgebühr ass fir ee Joer gratis).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/UoAQkwY.webp)

Wann Dir eng Bestellung mécht, bemierkt `prefer AMD` , an de Server mat AMD CPU wäert besser Leeschtung hunn.

An der folgender wäert ech Contabo's VPS als Beispill huelen fir ze demonstréieren wéi Dir Ären eegene Mailserver bauen.

## Ubuntu System Konfiguratioun

De Betribssystem hei ass Ubuntu 22.04

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/smpIu1F.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/m7Mwjwr.webp)

Wann de Server op ssh weist `Welcome to TinyCore 13!` (wéi an der Figur ënnendrënner gewisen), heescht et, datt de System nach net installéiert gouf. Trennt w.e.g. ssh un a waart e puer Minutten fir erëm anzeloggen.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/-qKACz9.webp)

Wann `Welcome to Ubuntu 22.04.1 LTS` erschéngt, ass d'Initialiséierung fäerdeg, an Dir kënnt mat de folgende Schrëtt weidergoen.

### [Optional] Initialiséieren d'Entwécklungsëmfeld

Dëse Schrëtt ass fakultativ.

Fir d'Bequemlechkeet hunn ech d'Installatioun an d'Systemkonfiguratioun vun der Ubuntu Software an [github.com/wactax/ops.os/tree/main/ubuntu](https://github.com/wactax/ops.os/tree/main/ubuntu) gesat.

Run de folgende Kommando fir mat engem Klick z'installéieren.

```
bash <(curl -s https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

Chinesesch Benotzer, benotzt w.e.g. de folgende Kommando amplaz, an d'Sprooch, d'Zäitzon, asw.

```
CN=1 bash <(curl -s https://ghproxy.com/https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

### Contabo erméiglecht IPV6

Aktivéiert IPV6 sou datt SMTP och E-Maile mat IPV6 Adressen schécken kann.

änneren `/etc/sysctl.conf`

Änneren oder addéieren déi folgend Zeilen

```
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.default.disable_ipv6 = 0
net.ipv6.conf.lo.disable_ipv6 = 0
```

Verfollegt mam [Contabo Tutorial: Füügt IPv6 Konnektivitéit op Äre Server](https://contabo.com/blog/adding-ipv6-connectivity-to-your-server/)

Änneren `/etc/netplan/01-netcfg.yaml` , füügt e puer Zeilen derbäi wéi an der Figur hei ënnendrënner (Contabo VPS Standard Konfiguratiounsdatei huet dës Linnen schonn, just decommentéieren se).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/5MEi41I.webp)

Dann `netplan apply` fir déi modifizéiert Konfiguratioun a Kraaft ze maachen.

Nodeems d'Konfiguratioun erfollegräich ass, kënnt Dir `curl 6.ipw.cn` benotzen fir d'IPv6 Adress vun Ärem externen Netzwierk ze gesinn.

## Klon d'Konfiguratiounsrepository ops

```
git clone https://github.com/wactax/ops.soft.git
```

## Generéiere e gratis SSL Zertifika fir Ären Domain Numm

E-Mail schécken erfuerdert en SSL Zertifika fir Verschlësselung an Ënnerschrëft.

Mir benotzen [acme.sh](https://github.com/acmesh-official/acme.sh) fir Certificaten ze generéieren.

acme.sh ass en Open Source automatiséiert Zertifika Ënnerschreiwe Tool,

Gitt d'Konfiguratiounslager ops.soft, lafen `./ssl.sh` , an e `conf` Ordner gëtt **am ieweschte Verzeechnes** erstallt.

Fannt Ären DNS Provider vun [acme.sh dnsapi](https://github.com/acmesh-official/acme.sh/wiki/dnsapi) , edit `conf/conf.sh` .

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Qjq1C1i.webp)

Da lafen `./ssl.sh 123.com` fir `123.com` an `*.123.com` Certificaten fir Ären Domain Numm ze generéieren.

Déi éischt Laf wäert automatesch [acme.sh](https://github.com/acmesh-official/acme.sh) installéieren an eng geplangte Aufgab fir automatesch Erneierung addéieren. Dir kënnt `crontab -l` gesinn, et gëtt sou eng Linn wéi follegt.

```
52 0 * * * "/mnt/www/.acme.sh"/acme.sh --cron --home "/mnt/www/.acme.sh" > /dev/null
```

De Wee fir de generéierten Zertifika ass eppes wéi `/mnt/www/.acme.sh/123.com_ecc。`

Zertifikat Erneierung nennt `conf/reload/123.com.sh` Skript, edit dëst Skript, Dir kënnt Kommandoen addéieren wéi `nginx -s reload` fir den Zertifikat Cache vun ähnlechen Uwendungen z'erfrëschen.

## Build SMTP Server mat Chasquid

[chasquid](https://github.com/albertito/chasquid) ass en Open Source SMTP Server geschriwwen an der Go Sprooch.

Als Ersatz fir déi antike Mail Server Programmer wéi Postfix a Sendmail, Chasquid ass méi einfach a méi einfach ze benotzen, an et ass och méi einfach fir sekundär Entwécklung.

Run `./chasquid/init.sh 123.com` gëtt automatesch mat engem Klick installéiert (ersetzt 123.com mat Ärem schéckt Domain Numm).

## E-Mail Ënnerschrëft DKIM konfiguréieren

DKIM gëtt benotzt fir E-Mail Ënnerschrëften ze schécken fir ze verhënneren datt Bréiwer als Spam behandelt ginn.

Nodeems de Kommando erfollegräich leeft, gitt Dir opgefuerdert den DKIM Rekord ze setzen (wéi hei ënnendrënner).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/LJWGsmI.webp)

Füügt just en TXT-Rekord op Är DNS un (wéi hei ënnendrënner).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/0szKWqV.webp)

## View Service Status & Logbicher

 `systemctl status chasquid` View Service Status.

Den Zoustand vun der normaler Operatioun ass wéi an der Figur hei ënnen gewisen

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/CAwyY4E.webp)

 `grep chasquid /var/log/syslog` oder `journalctl -xeu chasquid` kann de Feelerprotokoll kucken.

## Ëmgedréint Domain Numm Configuratioun

De ëmgedréint Domain Numm ass fir datt d'IP Adress op den entspriechende Domain Numm geléist gëtt.

En ëmgedréint Domain Numm ze setzen kann verhënneren datt E-Mailen als Spam identifizéiert ginn.

Wann d'Mail empfaangen ass, wäert den Empfangsserver ëmgedréint Domain Numm Analyse op der IP Adress vum Sender Server maachen fir ze bestätegen ob de Sender Server e gültege Reverse Domain Numm huet.

Wann de Sender Server keen ëmgedréint Domain Numm huet oder wann de Reverse Domain Numm net mat der IP Adress vum Sender Server entsprécht, kann den Empfangsserver d'E-Mail als Spam erkennen oder se refuséieren.

Besicht [https://my.contabo.com/rdns](https://my.contabo.com/rdns) a konfiguréiert wéi hei ënnendrënner

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/IIPdBk_.webp)

Nodeems Dir de Reverse Domain Numm gesat hutt, erënnert Iech un d'Forward Resolutioun vum Domain Numm ipv4 an ipv6 op de Server ze konfiguréieren.

## Änneren den Hostnumm vun chasquid.conf

Änneren `conf/chasquid/chasquid.conf` op de Wäert vum ëmgedréint Domain Numm.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/6Fw4SQi.webp)

Da lafen `systemctl restart chasquid` fir de Service nei ze starten.

## Backup conf zu Git Repository

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Fier9uv.webp)

Zum Beispill maachen ech den conf Dossier op mäin eegene Github Prozess wéi follegt

Erstellt als éischt e private Lager

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ZSCT1t1.webp)

Gitt de conf Verzeechnes a gitt an de Lager of

```
git init
git add .
git commit -m "init"
git branch -M main
git remote add origin git@github.com:wac-tax-key/conf.git
git push -u origin main
```

## Sender derbäi

lafen

```
chasquid-util user-add i@wac.tax
```

Kann Sender derbäi

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/khHjLof.webp)

### Vergewëssert Iech datt d'Passwuert richteg agestallt ass

```
chasquid-util authenticate i@wac.tax --password=xxxxxxx
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/e92JHXq.webp)

Nodeems Dir de Benotzer bäigefüügt hutt, ginn `chasquid/domains/wac.tax/users` aktualiséiert, erënnert Iech un de Lager ofzeginn.

## DNS add SPF Rekord

SPF (Sender Policy Framework) ass eng E-Mail Verifizéierungstechnologie déi benotzt gëtt fir E-Mail Bedruch ze vermeiden.

Et verifizéiert d'Identitéit vun engem E-Mail-Sender andeems Dir iwwerpréift datt d'IP Adress vum Sender mat den DNS-Recorde vum Domain Numm entsprécht, deen et behaapt ze sinn, verhënnert datt Bedrucher falsch E-Maile schécken.

SPF-Records derbäizefügen kann verhënneren datt E-Maile sou vill wéi méiglech als Spam identifizéiert ginn.

Wann Ären Domain Numm Server net den SPF Typ ënnerstëtzt, füügt just den TXT Typ Rekord un.

Zum Beispill ass de SPF vu `wac.tax` wéi follegt

`v=spf1 a mx include:_spf.wac.tax include:_spf.google.com ~all`

SPF fir `_spf.wac.tax`

`v=spf1 a:smtp.wac.tax ~all`

Notéiert datt ech `include:_spf.google.com` hei, dëst ass well ech `i@wac.tax` als Senderadress an der Google Mailbox spéider konfiguréieren.

## DNS Configuratioun DMARC

DMARC ass d'Ofkierzung vun (Domain-baséiert Message Authentication, Reporting & Conformance).

Et gëtt benotzt fir SPF-Bounces z'erfaassen (vläicht duerch Konfiguratiounsfehler verursaacht, oder een aneren mécht sech wéi Dir sidd fir Spam ze schécken).

Add TXT record `_dmarc` ,

Den Inhalt ass wéi follegt

```
v=DMARC1; p=quarantine; fo=1; ruf=mailto:ruf@wac.tax; rua=mailto:rua@wac.tax
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/k44P7O3.webp)

D'Bedeitung vun all Parameter ass wéi follegt

### p (Politik)

Gëtt un wéi Dir E-Mailen behandelt déi d'SPF (Sender Policy Framework) oder DKIM (DomainKeys Identified Mail) Verifizéierung feelen. De p-Parameter kann op ee vun dräi Wäerter gesat ginn:

* kee: Keng Handlung gëtt gemaach, nëmmen d'Verifizéierungsresultat gëtt un de Sender duerch den E-Mail Berichterstattungsmechanismus zréckgeschéckt.
* Quarantän: Setzt d'Mail, déi d'Verifizéierung net passéiert huet an de Spam-Dossier, awer refuséiert d'Mail net direkt.
* refuséieren: Direkt E-Maile refuséieren déi d'Verifizéierung feelen.

### fo (Feeleroptiounen)

Spezifizéiert d'Quantitéit un Informatioun, déi vum Berichtmechanismus zréckgeet. Et kann op ee vun de folgende Wäerter gesat ginn:

* 0: Rapport Validatioun Resultater fir all Messagen
* 1: Meld nëmmen Messagen déi d'Verifikatioun feelen
* d: Rapport nëmmen Domain Numm Verifikatioun Feeler
* s: Rapport nëmmen SPF Verifizéierungsfehler
* l: Rapport nëmmen DKIM Verifikatioun Feeler

### rua & ruf

* rua (Reporting URI fir Aggregate Berichter): E-Mail Adress fir aggregéiert Berichter ze kréien
* ruf (Reporting URI for Forensic Reports): E-Mail Adress fir detailléiert Berichter ze kréien

## Füügt MX-Records fir E-Mailen op Google Mail weiderzebréngen

Well ech keng gratis Firmen-Mailbox fonnt hunn déi universell Adressen ënnerstëtzt (Catch-All, kann all E-Mail kréien, déi op dësen Domain Numm geschéckt ginn, ouni Restriktiounen op Präfixe), hunn ech Chasquid benotzt fir all E-Mailen op meng Gmail Mailbox weiderzebréngen.

**Wann Dir Är eege bezuelte Business Mailbox hutt, ännert w.e.g. den MX net a spréngt dëse Schrëtt iwwer.**

Edit `conf/chasquid/domains/wac.tax/aliases` , setzen Forward Mailbox

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/OBDl2gw.webp)

`*` weist all E-Mailen un, `i` ass d'E-Mailadress Präfix vum schéckende Benotzer uewen erstallt. Fir Mail weiderzebréngen, muss all Benotzer eng Zeil derbäisetzen.

Füügt dann den MX-Rekord (ech weisen direkt op d'Adress vum ëmgedréint Domain Numm hei, wéi an der éischter Zeil an der Figur hei drënner).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/7__KrU8.webp)

Nodeems d'Konfiguratioun fäerdeg ass, kënnt Dir aner E-Mailadressen benotze fir E-Mailen op `i@wac.tax` an `any123@wac.tax` ze schécken fir ze kucken ob Dir E-Mailen a Gmail kritt.

Wann net, kontrolléiert de Chasquid Log ( `grep chasquid /var/log/syslog` ).

## Schéckt eng E-Mail op i@wac.tax mat Google Mail

Nodeems Google Mail d'Mail krut, hunn ech natierlech gehofft mat `i@wac.tax` anstatt i.wac.tax@gmail.com ze äntweren.

Besicht [https://mail.google.com/mail/u/1/#settings/accounts](https://mail.google.com/mail/u/1/#settings/accounts) a klickt op "Füügt eng aner E-Mailadress".

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/PAvyE3C.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/_OgLsPT.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/XIUf6Dc.webp)

Da gitt de Verifizéierungscode, deen Dir vun der E-Mail kritt hutt, op déi weidergeleet gouf.

Schlussendlech kann et als Standard Sender Adress gesat ginn (zesumme mat der Optioun fir mat der selwechter Adress ze äntweren).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/a95dO60.webp)

Op dës Manéier hu mir d'Grënnung vum SMTP Mail Server ofgeschloss a gläichzäiteg Google Mail benotze fir E-Mailen ze schécken an ze kréien.

## Schéckt eng Test-E-Mail fir ze kontrolléieren ob d'Konfiguratioun erfollegräich ass

Gitt `ops/chasquid`

Run `direnv allow` Ofhängegkeeten z'installéieren (direnv gouf am virege One-Schlëssel Initialiséierungsprozess installéiert an en Hook ass an d'Schuel bäigefüügt)

dann lafen

```
user=i@wac.tax pass=xxxx to=iuser.link@gmail.com ./sendmail.coffee
```

D'Bedeitung vun de Parameteren ass wéi follegt

* Benotzer: SMTP Benotzernumm
* pass: SMTP Passwuert
* zu: Empfänger

Dir kënnt eng Test E-Mail schécken.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ae1iWyM.webp)

Et ass recommandéiert Gmail ze benotzen fir Test-E-Mailen ze kréien fir ze kontrolléieren ob d'Konfiguratiounen erfollegräich sinn.

### TLS Standard Verschlësselung

Wéi an der Figur hei ënnendrënner, gëtt et dëse klenge Spär, dat heescht datt den SSL Zertifikat erfollegräich aktivéiert gouf.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/SrdbAwh.webp)

Da klickt op "Show Original Email"

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/qQQsdxg.webp)

### DKIM

Wéi an der Figur hei ënnen gewisen, weist d'Gmail Original Mail Säit DKIM, dat heescht datt d'DKIM Konfiguratioun erfollegräich ass.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/an6aXK6.webp)

Kuckt de Empfang am Header vun der ursprénglecher E-Mail, an Dir kënnt gesinn datt d'Sender Adress IPV6 ass, dat heescht datt IPV6 och erfollegräich konfiguréiert ass.
