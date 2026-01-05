---
title: "Osnovy Razvedki: Ot Syryh Dannyh k Deystvennoy Razvedke"
slug: "intelligence-fundamentals-part-0"
date: 2026-01-05
draft: false
series: "Osnovy Razvedki"
series_order: 0
tags: ["intelligence", "OSINT", "SIGINT", "HUMINT", "threat-intelligence", "CTI", "fundamentals", "analysis"]
categories: ["Intelligence"]
keywords: ["intelligence cycle", "DIKW pyramid", "threat intelligence", "OSINT", "SIGINT", "HUMINT", "source reliability", "cognitive bias", "attribution"]
description: "Kompleksnoe pogruzhenie v osnovy razvedyvatelnoy deyatelnosti. Ponimanie konveyera dannyh-k-mudrosti, distsiplin sbora (INT), razvedyvatelnogo tsikla, otsenki istochnikov, kognitivnyh predraspolozheniy i kak eti kontseptsii primenyayutsya k sovremennoy kiberrazvedke."
author: "burnedsignal"
toc: true
reading_time: "25 min"
---

## TL;DR

Razvedka — eto ne prosto sbor dannyh — eto sistematicheskoe preobrazovanie syroy informatsii v deystvennye vyvody, kotorye upravlyayut prinyatiem resheniy. Eta osnovopolagayushchaya statya okhvatyvaet:

- **Piramida DIKW**: Progressiya Dannye → Informatsiya → Znaniya → Mudrost
- **Urovni Razvedki**: Strategicheskie, Operativnye i Takticheskie razlichiya
- **Distsipliny Sbora (INT)**: HUMINT, SIGINT, OSINT, GEOINT, MASINT, FININT
- **Razvedyvatelnyy Tsikl**: Nepreryvnyy 6-faznyy protsess ot trebovaniy do obratnoy svyazi
- **Otsenka Istochnikov**: Sistema Admiralteystva dlya otsenki nadezhnosti i dostovernosti
- **Kognitivnye Predraspolozheniya**: Tikhie ubiyty kachestvennogo razvedyvatelnogo analiza
- **Problemy Atributsii**: Pochemu "kto eto sdelal" slozhnee, chem kazhetsya
- **Primenenie k Kiberrazvedke (CTI)**: Kak traditsionnye razvedyvatelnye kontseptsii sootnosyatsya s sovremennym obnaruzheniem ugroz

---

## Chto Takoe Razvedka?

Razvedka chasto nepravilno ponimaetsya. Eto ne prosto nakoplenie faktov i ne sinonim dannyh ili informatsii. Razvedka predstavlyaet soboy **ochishchennyy produkt** strogogo analiticheskogo protsessa — preobrazovanie razroznennyh tochek dannyh v kogerentnoe, kontekstnoe ponimanie, kotoroe pozvolyaet prinimat obosnovannye resheniya.

> "Razvedka — eto informatsiya, kotoraya byla sobrana, integrirována, otsenena, proanalizirovana i interpretirována."
> — Joint Publication 2-0, Joint Intelligence

Eto razlichie vazhno. Datchik, obnaruzhivayushchiy setevoy trafik, proizvodit **dannye**. Kogda eti dannye razbiraet i korreliruet, oni stanovyatsya **informatsiey**. Kogda analitik opredelyaet, chto pattern trafika sootvetstvuet izvestnomu povedeniyu komandno-kontrolnogo servera, eto stanovitsya **znaniyami**. Kogda rukovodstvo ispolzuet eti znaniya dlya avtorizatsii zashchitnyh mer ili atributsii aktivnosti konkretnomu aktyoru ugroz, my priblizaemsya k **mudrosti**.

**Kriticheskiy Moment**: Razvedka — eto ne istina — eto *otsenka* istiny na osnove nepolnoy informatsii. Kazhdyy razvedyvatelnyy produkt neset prisushchuyu neopredelennost, poetomu urovni doveriya i otsenka istochnikov yavlyayutsya fundamentalnymi dlya distsipliny.

---

## Piramida DIKW: Ot Shuma k Insaytu

Ierarkhiya Dannye-Informatsiya-Znaniya-Mudrost (DIKW) predostavlyaet fundamentalnuyu strukturu dlya ponimaniya togo, kak syrye vhody transformiruyutsya v deystvennuyu razvedku.

![DIKW Pyramid](/images/intelligence-fundamentals/dikw-pyramid.svg)

### Dannye (Chto?)

Syrye, neobrabotannye fakty bez konteksta. V kiberoperatsiyah eto vklyuchaet:
- Zakhvaty paketov
- Zapisi logov
- Heshi faylov
- IP-adresa
- DNS-zaprosy

Odni dannye otvechayut na vopros "Chto proizoshlo?", no ne predostavlyayut smysla.

**Primer**: `192.168.1.105 connected to 45.33.32.156:443 at 03:42:17 UTC`

### Informatsiya (Kto, Kogda, Gde?)

Dannye stanovyatsya informatsiey, kogda organizovany, strukturirovany i poluchayut kontekst. Informatsiya otvechaet na bazovye voprosy.

**Primer**: "Rabochaya stantsiya WS-105, naznachennaya polzovatelyu john.doe v finansovom otdele, ustanovila zashifrovannoe soedinenie s IP-adresom, gelokalizovannym v Sankt-Peterburge, Rossiya, v nerabochee vremya."

### Znaniya (Kak? Pochemu?)

Znaniya voznikayut iz analiza patternov, korrelyatsii neskolkih istochnikov informatsii i primeneniya ekspertizy. Oni obespechivayut ponimanie mekhanizmov i motivatsiy.

**Primer**: "Etot pattern soedineniya sootvetstvuet izvestnomu povedeniyu mayaka Cobalt Strike. IP-adres naznacheniya svyazan s infrastrukturoy, ranee atributirovannoy APT29. Finansovyy otdel imeet dostup k sensityvnoy dokumentatsii M&A. Veroyatno, eto predstavlyaet nachalnyy dostup sofistizirovannogo aktyora ugroz, osushchestvlyayushchego ekonomicheskiy shpionazh."

### Mudrost (Chto Nam Delat?)

Mudrost sinteziruet znaniya s opytom, eticheskimi soobrazheniyami i strategicheskim kontekstom dlya informirovaniya resheniy. Ona obespechivaet predvidenie i optimalnyy vybor deystviy.

**Primer**: "Na osnove urovnya doveriya atributsii (UMERENNYY), potentsialnogo vozdeystviya na biznes i geopoliticheskogo konteksta my rekomenduem: (1) nemedlennuyu setevuyu izolyatsiyu zatronutyh sistem, (2) privlechenie gruppy reagirovaniya na intsidenty, (3) uvedomlenie yuridicheskogo konsultanta otnositelno potentsialnogo uchastiya natsionalnogo gosudarstva, (4) koordinatsiyu s partnerami po ISAC sektora, kotorye mogut stolknutsya s analogichnym targetingom."

---

## Chetyre Kachestva Deystvennoy Razvedki

Ne vsya razvedka sozdana ravnoy. Effektivnaya razvedka demonstriruet chetyre kriticheskie kharakteristiki:

| Kachestvo | Opredelenie | Kontrprimer |
|-----------|-------------|-------------|
| **Deystvennaya** | Pozvolyaet prinimat konkretnye resheniya ili deystviya | "Plohie aktory sushchestvuyut v internete" |
| **Svoevremennaуa** | Dostavlena kogda ona eshche mozhet povliyat na rezultaty | Otchet ob atributsii, pribyvayushchiy cherez 6 mesyatsev posle vzloma |
| **Relevantnaya** | Otvechaet konkretnym trebovaniyam potrebitelya | Otpravka razvedki ugroz ICS/SCADA kompanii SaaS |
| **Tochnaya** | Fakticheski pravilnaya s sootvetstvuyushchimi urovnyami doveriya | Oshibochnaya atributsiya tovarnogo vredonosnogo PO k APT |

Akronim **ATRA** (Actionable, Timely, Relevant, Accurate) predostavlyaet poleznuyu mnemoniku dlya otsenki kachestva razvedki.

---

## Otsenka Istochnikov: Sistema Admiralteystva

![Source Reliability Matrix](/images/intelligence-fundamentals/source-reliability-matrix.svg)

Prezhde chem lyubaya informatsiya stanet razvedkoy, ona dolzhna byt otsenena. Razvedyvatelnoe soobshchestvo ispolzuet standartizirovannye sistemy gradatsii dlya otsenki kak **nadezhnosti istochnika**, tak i **dostovernosti informatsii**.

### Sistema Admiralteystva/NATO

Eta dvuhosevaya sistema otsenki, takzhe izvestnaya kak "sistema 6x6" ili "Sistema NATO," yavlyaetsya standartom so vremeni Vtoroy mirovoy voyny:

#### Nadezhnost Istochnika (A-F)

| Otsenka | Opisanie | Kriterii |
|---------|----------|----------|
| **A** | Polnostyu Nadezhnyy | Net somneniy v podlinnosti, nadezhnosti i kompetentnosti istochnika. Istoriya polnoy nadezhnosti. |
| **B** | Obychno Nadezhnyy | Neznachitnye somneniya. Istoriya v osnovnom dostovernoy informatsii. |
| **C** | Dostatochno Nadezhnyy | Somneniya sushchestvuyut. Predostavlyal dostovernuyu informatsiyu v proshlom. |
| **D** | Obychno Nenadezhnyy | Znachitelnye somneniya. Istoriya chastichno dostovernoy, chastichno nedostovernoy informatsii. |
| **E** | Nenadezhnyy | Otsutstvuet podlinnost, nadezhnost ili kompetentnost. Istoriya nedostovernoy informatsii. |
| **F** | Nevozmozhno Otsenyt | Net osnovaniya dlya otsenki nadezhnosti. Novyy ili neizvestnyy istochnik. |

#### Dostovernost Informatsii (1-6)

| Otsenka | Opisanie | Kriterii |
|---------|----------|----------|
| **1** | Podtverzhdeno | Podtverzhdeno nezavisimymi istochnikami. Logichno, soglasovano s drugoy informatsiey. |
| **2** | Veroyatno Istinno | Ne podtverzhdeno, no logichno i soglasovano s drugoy informatsiey. |
| **3** | Vozmozhno Istinno | Ne podtverzhdeno. Dostatochno logichno, no ogranichennoе podtverzhdenie. |
| **4** | Somnitelno Istinno | Ne podtverzhdeno. Vozmozhno, no ne logichno. Net drugoy informatsii dlya podderzhki. |
| **5** | Maloveroyatno | Ne podtverzhdeno. Ne logichno. Protivorechit drugoy informatsii. |
| **6** | Nevozmozhno Otsenyt | Net osnovaniya dlya otsenki dostovernosti. |

### Prakticheskoe Primenenie

**Primer Otsenki:**

```
Istochnik: Polzovatel andergrand-foruma "xShadowBrokerx" (aktiven 3 goda,
        podtverzhdennaya istoriya prodazh, ranee tochnye utechki)
Informatsiya: Zayavlyaet o predstoyashchey kampanii ransomware, napravlennoy na sektor zdravookhraneniya

Otsenka: B2
- Nadezhnost Istochnika: B (Obychno Nadezhnyy) - Ustanovlennoe prisutstvie, istoriya
- Dostovernost Informatsii: 2 (Veroyatno Istinno) - Soglasovano s nablyudaemym
  landshaftom ugroz, ne podtverzhdeno nezavisimo
```

**Pochemu Eto Vazhno**: Bez otsenki istochnika vy ne mozhete naznachit urovni doveriya vashim otsenkam. Analitik, kotoryy obrashchaetsya s istochnikom B1 tak zhe, kak s istochnikom E5, budet proizvodit musornuyu razvedku.

---

## Kognitivnye Predraspolozheniya: Tikhie Ubiyty

![Cognitive Biases](/images/intelligence-fundamentals/cognitive-biases.svg)

Neudachi razvedki redko svyazany s nedostatkom informatsii — oni svyazany s neudachami v analize. Kognitivnye predraspolozheniya — eto sistematicheskie oshibki v myshlenii, kotorye vliyayut na resheniya i suzhdeniya.

> "My vidim to, chto ozhidaem uvidet, a ne to, chto est na samom dele."
> — Richards Heuer, *Psychology of Intelligence Analysis*

### Kriticheskie Predraspolozheniya dlya Razvedyvatelnyh Analitikov

#### Predraspolozheniye Podtverzhdeniya
**Opredelenie**: Poisk, interpretatsiya i zapominanie informatsii, kotoraya podtverzhdaet ranee sushchestvuyushchie ubezhdeniya.

**Istoricheskiy Primer**: Otsenka OMP Iraka (2002-2003). Analitiki sosredotochilis na informatsii, podderzhivayushchey sushchestvovanie programm OMP, otbrasyvaya protivorechivye dokazatelstva.

**Snizhenie**: Advokatiya Dyavola, Analiz Konkuriruyushchikh Gipotez (ACH)

---

#### Predraspolozheniye Yakorya
**Opredelenie**: Chrezmernaya zavisimost ot pervoy poluchennoy informatsii ("yakorya").

**Primer v CTI**: Nachalnaya atributsiya konkretnomu aktyoru ugroz stanovitsya yakorem. Vse posleduyushchie dokazatelstva interpretiruyutsya cherez etu prizmu.

**Snizhenie**: Yavno dokumentirovat nachalnye predpolozheniya, regulyarno ih peresmotrivat

---

#### Zerkalnoye Otrazheniye
**Opredelenie**: Predpolozhenie, chto protivniki dumayut i deystvuyut tak zhe, kak my by deystvovali v ikh situatsii.

**Istoricheskiy Primer**: Pearl Harbor (1941). Amerikanskie analitiki predpolagali, chto Yaponiya ne atachuet, potomu chto eto bylo by "neratsionalno" s uchetom voennogo prevoskhodstva SShA.

**Snizhenie**: Analiz Krasnoy Komandy, Kulturnaya ekspertiza

---

#### Gruppovoe Myshlenie
**Opredelenie**: Davlenie konformizma vnutri gruppy podavlyaet alternativnyy analiz.

**Istoricheskiy Primer**: Zaliv Sviney (1961). Planirovshchiki TsRU ubedili sebya, chto vtorzhenie uspeet; nesoglasnyye golosa byli zaglusheny.

**Snizhenie**: Strukturirovannoye nesoglasie (naznachenie roli "advokata dyavola")

---

#### Evristika Dostupnosti
**Opredelenie**: Pereotsenivaniye informatsii, kotoraya legko prikhodit na um (nedavnie, dramaticheskie sobytiya).

**Primer v CTI**: Posle gromkoy ataki ransomware analitiki mogut chrezymerno atributirovat posleduyushchiye intsidenty tomu zhe aktyoru.

**Snizhenie**: Analiz bazovoy stavki, strukturirovannye chek-listy

---

### Strukturirovannye Analiticheskie Tekhniki (SAT)

Razvedyvatelnoe soobshchestvo razrabotalo SAT spetsialno dlya protivodeystviya kognitivnym predraspolozheniyam:

| Tekhnika | Tsel | Kogda Ispolzovat |
|----------|------|------------------|
| **Analiz Konkuriruyushchikh Gipotez (ACH)** | Sistematicheski otsenit neskolko obyasneniy protiv dokazatelstv | Atributsiya, slozhnye otsenki |
| **Proverka Klyuchevykh Predpolozheniy** | Vyyavit i issledovat bazovye predpolozheniya | Lyubaya krupnaya otsenka |
| **Analiz Krasnoy Komandy** | Dumat kak protivnik | Otsenka ugroz, analiz uyazvimostey |
| **Advokatiya Dyavola** | Argumetiravat protiv preobladayushchey tochki zreniya | Pered finalizatsiey otsenok |
| **Indikatory i Preduprezhdeniya (I&W)** | Opredelit nablyudaemye sobytiya, signaliziruyushchiye ob izmenenii | Monitoring, prognozirovanie |

---

## Urovni Razvedki: Strategicheskaya, Operativnaya, Takticheskaya

Razvedyvatelnye trebovaniya i produkty znachitelno razlichayutsya v zavisimosti ot gorizonta prinyatiya resheniy potrebitelya.

![Intelligence Levels](/images/intelligence-fundamentals/intelligence-levels.svg)

### Strategicheskaya Razvedka
- **Auditoriya**: Rukovoditeli, Sovet Direktorov, Politiki
- **Gorizont**: 12-36 mesyatsev
- **Fokus**: Trendy landshafta ugroz, pozitsiya riska, investitsionnye prioritety
- **Primer**: "Aktyory natsionalnykh gosudarstv vse chashche targetiruyut tsepochki postavok v nashem sektore. My rekomenduem diversifikatsiyu kriticheskikh postavshchikov."

### Operativnaya Razvedka
- **Auditoriya**: Menedzhery Bezopasnosti, Komandy IR, Komandy Okhoty
- **Gorizont**: Nedeli do mesyatsev
- **Fokus**: Analiz kampaniy, profili aktorov ugroz, evolyutsiya TTP
- **Primer**: "APT41 perešel ot kastamnogo vredonosnogo PO k metodam living-off-the-land v Q4. Komandy okhoty dolzhny prioritiziravat detektsiyu zloupotrebleniya LOLBins."

### Takticheskaya Razvedka
- **Auditoriya**: Analitiki SOC, Inzhenery Detektsii, Spetsialisty po Reagirovaniyu
- **Gorizont**: Chasy do dney
- **Fokus**: IOC, signatury detektsii, konkretnye TTP
- **Primer**: "Zablokirovat hash `abc123...`, IP `45.33.32.156`, i monitorit sozdanie zaplanirovannyh zadach s pomoshchyu `schtasks.exe /create`."

**Kriticheskiy Vyvod**: Organizatsii chasto pereinvestiruyut v takticheskuyu razvedku (fidy IOC), nedoinvestiruya v strategicheskuyu i operativnuyu razvedku. IOC po svoey prirode nedolgovechny — oni predstavlyayut *artefakty* ataki, a ne *povedenie*.

---

## Distsipliny Sbora Razvedki ("INT")

Sbor razvedki organizovan v spetsializirovannye distsipliny, kazhdaya s razlichnymi istochnikami, metodami i analitichekimi trebovaniyami.

![Intelligence Disciplines](/images/intelligence-fundamentals/intelligence-disciplines.svg)

### Osnovnye Distsipliny Sbora

#### HUMINT (Chelovecheskaya Razvedka)

**Opredelenie**: Razvedka, poluchennaya ot chelovecheskikh istochnikov cherez mezhlichnostnyy kontakt.

**Istochniki**:
- Informatory i agenty
- Diplomaticheskaya otchetnost
- Debrifingi puteshestvennikov
- Perebezhchiki
- Doprosy
- Elitsitatsiya (sotsialnaya inzheneriya v kibersmontekste)

**Kharakteristiki**:
- Drevneyshaya razvedyvatelnaya distsiplina
- Predostavlyaet namereníya i motivatsiyu ("pochemu")
- Vysokaya tsennost, vysokiy risk
- Trudno masshtabirovat

**Kiberprimenenie**: Vzaimodeystvie s aktorami ugroz na andergrand-forumakh, verbovka istochnikov vnutri prestupnykh organizatsiy, otsenki sotsialnoy inzhenerii.

---

#### SIGINT (Signalnaya Razvedka)

**Opredelenie**: Razvedka, poluchennaya iz perekhvata signalov, vklyuchaya kommunikatsii i elektronnye izlucheniya.

**Poddistsipliny**:

| Abbreviatura | Nazvanie | Fokus |
|--------------|----------|-------|
| **COMINT** | Kommunikatsionnaya Razvedka | Golos, tekst, soobshcheniya |
| **ELINT** | Elektronnaya Razvedka | Nekommunikatsionnye signaly (radar, telemetriya) |
| **FISINT** | Razvedka Inostrannykh Instrumentalnykh Signalov | Sistemy vooruzheniy, telemetriya kosmicheskikh apparatov |

**Kharakteristiki**:
- Tekhnicheskiy, masshtabiruyemyy sbor
- Obem sozdaet analiticheskie vyzovy
- Shifrovanie — znachitelnoye prepyatstvie

**Kiberprimenenie**: Analiz setevogo trafika, analiz protokolov C2 vredonosnogo PO, analiz metadannyh zashifrovannogo trafika.

---

#### OSINT (Razvedka Otkrytykh Istochnikov)

**Opredelenie**: Razvedka, poluchennaya iz publichno dostupnykh istochnikov.

**Istochniki**:
- Media (pechat, veshchanie, onlayn)
- Akademicheskie publikatsii
- Pravitelstvennye otchety
- Sotsialnye seti
- Kommercheskie bazy dannyh
- Tekhnicheskaya dokumentatsiya

**Kharakteristiki**:
- Dostupna i ekonomicheski effektivna
- Obem trebuet sofistisirovannoy filtratsii
- Problemy verifikatsii (dezinformatsiya)
- Otsenochno obespechivaet 60-80% razvedyvatelnykh trebovaniy

**Kiberprimenenie**: Issledovanie aktorov ugroz, razvedka uyazvimostey, monitoring utechek uchetnyh dannyh, zashchita brenda, kartografirovanie poverkhnosti ataki.

---

#### GEOINT (Geoprostranstvennaya Razvedka)

**Opredelenie**: Razvedka, poluchennaya iz analiza izoobrazheniy i geoprostranstvennoy informatsii.

**Istochniki**:
- Sputnikovye snimki
- Aerofotosyemka
- Dannye kartografii
- Sluzhby na osnove mestopolozheniya
- Metadannye geolokatsii

**Kiberprimenenie**: Kartografirovanie fizicheskoy infrastruktury, identifikatsiya tsentrov obrabotki dannyh, verifikatsiya tsepochki postavok, podderzhka atributsii.

---

#### MASINT (Izmeritelnaya i Signaturnaya Razvedka)

**Opredelenie**: Razvedka, poluchennaya iz analiza dannyh, poluchennykh ot izmiritelnykh priborov dlya identifikatsii otlichitelnykh osobennostey istochnika.

**Oblasti Fokusa**:
- Radarnye signatury
- Akusticheskie signatury
- Yadernoe izluchenie
- Khimicheskoe/biologicheskoe detektirovaniye

**Kiberprimenenie**: Analiz elektromagnitnykh izlucheniy (TEMPEST), ataki po pobochnym kanalam, otpechatki apparatnogo obespecheniya.

---

### Dopolnitelnye Distsipliny

| INT | Polnoye Nazvanie | Fokus |
|-----|------------------|-------|
| **FININT** | Finansovaya Razvedka | Denezhnye tranzaktsii, obhod sanktsiy, otmyvanie deneg, otslezhivanie kriptovalyut |
| **SOCMINT** | Razvedka Sotsialnykh Setey | Analiz sotsialnykh platform, operatsii vliyaniya |
| **TECHINT** | Tekhnicheskaya Razvedka | Analiz inostrannogo vooruzheniya, revers-inzhiniring vredonosnogo PO |
| **IMINT** | Razvedka Izoobrazheniy | Podmnozhestvo GEOINT, sfokusirovannoe na analize izoobrazheniy |
| **MEDINT** | Meditsinskaya Razvedka | Razvedka, svyazannaya so zdorovyem, biologicheskie ugrozy |

---

## Razvedyvatelnyy Tsikl

Razvedyvatelnyy tsikl opisyvaet protsess, posredstvom kotorogo syraya informatsiya preobrazuetsya v gotovuyu razvedku i dostavlyaetsya potrebitelyam.

![Intelligence Cycle](/images/intelligence-fundamentals/intelligence-cycle.svg)

### Faza 1: Trebovaniya (Planirovanie i Rukovodstvo)

Razvedka nachinaetsya s voprosa. Trebovaniya opredelyayut:
- **Prioritetnye Razvedyvatelnye Trebovaniya (PIR)**: Kriticheskie voprosy, na kotorye rukovodstvo nuzhdaetsya v otvetakh
- **Sushchestvennye Elementy Informatsii (EEI)**: Konkretnye tochki dannyh, neobkhodimye dlya resheniya PIR
- **Aktsent sbora**: Kakie INT prioritiziravat
- **Razvedyvatelnye Probely**: Chto my ne znaem, no dolzhny znat

**Primer PIR**: "Kakie aktyory ugroz s naibolshey veroyatnostyu budut targetirovat nashu organizatsiyu v sleduyushchie 12 mesyatsev, i kakie TTP oni ispolzuyut?"

### Faza 2: Sbor

Sistematicheskiy sbor syroy informatsii s ispolzovaniem sootvetstvuyushchikh INT. Effektivnyy sbor:
- Sootvetstvuet opredelennym trebovaniyam
- Ispolzuet neskolko INT dlya podtverzhdeniya
- Dokumentiruet istochniki i metody
- Podderzhivayet tsepichu khraneniya

### Faza 3: Obrabotka (Ekspluatatsiya)

Syroy sobrannyy material preobrazuetsya v ispolzuyemuyu formu:
- Perevod
- Deshifrovanie
- Konversiya formata
- Normalizatsiya dannyh
- Deduplikatsiya
- Nacalnaya validatsiya
- Otsenka istochnika

### Faza 4: Analiz (Proizvodstvo)

Intellektualnoye yadro razvedyvatelnoy raboty. Analiz vklyuchaet:
- Korrelyatsiyu neskolkikh istochnikov
- Raspoznavanie patternov
- Razrabotku i testirovanie gipotez
- Primenenie Strukturirovannykh Analiticheskikh Tekhnik (SAT)
- Otsenku nadezhnosti i dostovernosti
- Naznachenie urovnya doveriya
- Proizvodstvo gotovykh razvedyvatelnykh produktov

### Faza 5: Rasprostraneníye

Gotovaya razvedka dolzhna dostignut potrebiteley v sootvetstvuyushchikh formatakh:
- Pismennye otchety
- Opoveshcheniya i uvedomleniya
- Mashino-chitaemye fidy (STIX/TAXII)
- Dashbordy i vizualizatsii
- Ustnye brifingi

**Klyuchevoy Printsip**: Razvedka, kotoraya ne dostigaet pravil'nogo cheloveka v nuzhnoye vremya, bespolezna.

### Faza 6: Obratnaya Svyaz (Otsenka)

Potrebiteli predostavlyayut obratnuyu svyaz o:
- Relevantnosti dlya ikh potrebnostey
- Svovremennosti dostavki
- Deystviennosti rekomendatsiy
- Tochnosti (so vremenem)

Eta obratnaya svyaz utochnyaet budushchiye trebovaniya, zamykaya tsikl.

---

## Atributsiya: Spektr Doveriya

Atributsiya — opredelenie, kto otvetsven za deystviye — yavlyaetsya odnoy iz samykh slozhnykh aspektov razvedyvatelnoy raboty, osobenno v kiberdomene.

### Pochemu Atributsiya Slozhna

| Vyzov | Opisanie |
|-------|----------|
| **Lozhnye Flagi** | Protivniki namerenno podkladyvayut dokazatelstva, ukazyvayushchiye na drugikh |
| **Obshchiy Instrumentariy** | Neskolko aktorov ispolzuyut odni i te zhe semeystva vredonosnogo PO (Cobalt Strike, Mimikatz) |
| **Proksi-Operatsii** | Podryadchiki, prestupniki po naymu skryvayut istinnykh sponsorov |
| **Peresechenie Infrastruktury** | Obshchiy khosting, bulletproof-provaydery, skomprometirovannye sistemy |

### Urovni Doveriya Atributsii

| Uroven | Opisanie | Osnova |
|--------|----------|--------|
| **VYSOKIY** | My otsenivaem s vysokim doveriem... | Neskolko nezavisimykh istochnikov, podtverzhdayushchiye dokazatelstva cherez INT |
| **UMERENNYY** | My otsenivaem s umerennym doveriem... | Horoshiye dokazatelstva ot menshego chisla istochnikov, nekotorye analiticheskie probely |
| **NIZKIY** | My otsenivaem s nizkim doveriem... | Ogranichennyye dokazatelstva, znachitelnye probely, pravdopodobno, no nepredelenno |

**Kriticheskiy Moment**: Dazhe "VYSOKOYE doveriye" — eto ne uverennost. NIE 2002 goda po OMP Iraka otsenivala s "vysokim doveriyem", chto Irak imel programmy OMP — i oshiblas.

---

## Primeneniye k Kiberrazvedke (CTI)

Traditsionnaya razvedyvatelnaya struktura napryamuyu otobrazhaetsya na kiberrazvedku:

| Traditsionnaya Kontseptsiya | Primeneniye CTI |
|----------------------------|-----------------|
| PIR | "Kakie gruppy ransomware targetiruyut nash sektor?" |
| HUMINT | Vzaimodeystviye s forumami dark web, programmy vnutrennikh ugroz |
| SIGINT | Analiz setevogo trafika, issledovanie protokolov C2 |
| OSINT | Blogi aktorov ugroz, paste-sayty, repozitorii koda |
| GEOINT | Geolokatsiya infrastruktury, sootvetstviye rezidentnosti dannyh |
| Otsenka Istochnikov | Otsenka kachestva fidov ugroz, doveriye k indikatoram |

### Piramida Boli

Piramida Boli Devida Bianko (2013) illyustriruet svyaz mezhdu tipami indikatorov i zatratami dlya protivnikov, kogda eti indikatory otkloyyeny:

![Pyramid of Pain](/images/intelligence-fundamentals/pyramid-of-pain.svg)

| Uroven | Tip Indikatora | Bol Protivnika | Primechaniya |
|--------|----------------|----------------|--------------|
| **Verh** | TTP | TYAZHELO! | Dolzhen izmenit povedenie, remeslo |
| | Instrumenty | Slozhno | Dolzhen nayti/razrabotat novye instrumenty |
| | Artefakty Hosta | Razdrazhayushche | Klyuchi reestra, puti faylov, myuteksy |
| | Setevye Artefakty | Razdrazhayushche | Patterny URI, protokoly C2, JA3 |
| | Domennye Imena | Prosto | DNS deshevo, no trebuet nastroyki |
| | IP-Adresa | Legko | Infrastruktura vzaimozamenyaema |
| **Niz** | Hash-Znacheniya | Trivialno | Rekompilirovat = novyy hash |

**Klyuchevoy Vyvod**: Organizatsii, kotorye fokusiruyut detektsiyu na Taktikah, Tekhnikakh i Protsedurakh (TTP), a ne na atomnykh indikatorakh, sozdayut znachitelno bolshiye zatraty dlya protivnikov. Eto soglasovano s podkhodom MITRE ATT&CK na osnove povedeniya.

---

## Case Study: Ot Dannyh k Razvedke

Proslim, kak syrye dannye stanovyatsya deystvennoy razvedkoy cherez pravilnyy analyticheskiy protsess:

### Dannye

```
timestamp: 2024-11-15T03:42:17Z
src_ip: 192.168.1.105
dst_ip: 45.33.32.156
dst_port: 443
bytes_out: 2847
bytes_in: 156892
```

### Informatsiya

- **Istochnik**: Rabochaya stantsiya WS-105 (Finansy, polzovatel: john.doe)
- **Naznachenie**: IP gelokalizovan v Sankt-Peterburge, Rossiya
- **Provajder**: VPS-provajder s dokumentirovannoy istoriey zloupotreblenij
- **Vremya**: 03:42 UTC (nerabochiye chasy dlya vostochnogo poberezh'ya SShA)
- **Pattern trafika**: Malen'kiy zapros, bolshoy otvet (vozmozhnaya eksfiltraciya ili check-in mayaka)

### Protsess Analiza

**Shag 1: Otsenka Istochnika**
- Setevaya telemetriya: A1 (nashi sobstvennye sensory, syrye dannye)
- Razvedka ugroz po IP naznacheniya: B2 (avtoritetnyy vendor, ne podtverzhdeno nezavisimo)
- Otchetnost ISAC: C2 (otchety kolleg, ogranichennaya detalizatsiya)

**Shag 2: Generatsiya Gipotez**
1. Vredonosnaya kommunikatsiya C2 (APT)
2. Vredonosnaya kommunikatsiya C2 (kriminal)
3. Legitimnaya, no neobychnaya biznes-aktivnost
4. Skomprometirovannoye storonneye prilozheniye
5. Lozhnopolozhitelnyy rezultat (CDN, oblachnyy servis)

**Shag 3: Otsenka Dokazatelstv**

| Dokazatelstvo | H1 (APT) | H2 (Kriminal) | H3 (Legit) | H4 (Storonneye) | H5 (FP) |
|---------------|----------|---------------|------------|-----------------|---------|
| Rossiyskiy IP | ++ | + | - | N | N |
| Nerabochiye chasy | + | + | - | N | N |
| Pattern trafika | ++ | ++ | N | + | - |
| Predydushchiy spearfishing | ++ | + | -- | N | -- |
| Korrelyatsiya ISAC | ++ | N | -- | N | -- |
| Targeting finansovogo otdela | ++ | + | N | N | N |

**Shag 4: Otsenka**

Posle primeneniya metodologii ACH:

> My otsenivaem s **UMERENNYM doveriyem**, chto eta aktivnost predstavlyaet kommunikatsiyu komandno-kontrolnogo servera sofistisirovannym aktorom ugroz, veroyatno APT29 ili affilirovannoy sushchnostyu. Eta otsenka osnovana na: korrelyatsii infrastruktury s ranee atributirovannymi operatsiyami (B2), sootvetstvii TTP dokumentirovannym playbukam natsional-gosudarstvennykh aktorov, i soglasovannosti targetinga s tselyami ekonomicheskogo shpionazha.
>
> **Alternativnye gipotezy**: Kriminalnyy aktor (vozmozhen, no menee soglasovan s targetingom), skomprometirovannoye storonneye PO (trebuet dopolnitelnogo rassledovaniya).

### Mudrost

**Rekomendatsii**:
1. Initsiirovat protsedury reagirovaniya na intsidenty (VYSOKIY prioritet)
2. Sokhranit kriminalisticheskie dokazatelstva dlya potentsialnogo vzaimodeystviya s pravoohranitelnymi organami
3. Privlech yuridicheskogo konsultanta otnositelno posledstviy atributsii natsionalnomu gosudarstvu
4. Koordinirovat s ISAC sektora
5. Brifingovat ispolnitelnoye rukovodstvo o potentsialnom vozdeystvii na biznes
6. Rasshirit detektsiyu dlya svyazannykh TTP po vsey organizatsii

---

## Vzglyad Vpered

Eta osnovopolagayushchaya statya ustanavlivaet kontseptualnuyu strukturu dlya razvedyvatelnykh operatsiy. Posleduyushchiye stati v etoy serii predostavyat boleye glubokiye pogruzheniya:

- **Chast 1**: Glubokoe Pogruzhenie v OSINT — Istochniki, Instrumenty, Remeslo i Pravovye Soobrazheniya
- **Chast 2**: Osnovy SIGINT — Ot RF do Paketa
- **Chast 3**: HUMINT v Kiberoperatsiyakh — Sotsialnaya Inzheneriya i Dalye
- **Chast 4**: GEOINT dlya Kibera — Fiziko-Tsifrovaya Konvergentsiya
- **Chast 5**: Strukturirovannyy Analiz — ACH, Red Teaming i Snizhenie Kognitivnykh Predraspolozheniy

---

## Glossariy

| Termin | Opredelenie |
|--------|-------------|
| **ACH** | Analiz Konkuriruyushchikh Gipotez — strukturirovannaya tekhnika dlya otsenki neskolkikh obyasneniy |
| **COMINT** | Kommunikatsionnaya Razvedka — podmnozhestvo SIGINT |
| **CTI** | Kiberrazvedka Ugroz |
| **EEI** | Sushchestvennye Elementy Informatsii |
| **ELINT** | Elektronnaya Razvedka — podmnozhestvo SIGINT |
| **FININT** | Finansovaya Razvedka |
| **GEOINT** | Geoprostranstvennaya Razvedka |
| **HUMINT** | Chelovecheskaya Razvedka |
| **I&W** | Indikatory i Preduprezhdeniya |
| **IOC** | Indikator Kompromtatsii |
| **ISAC** | Tsentr Obmena i Analiza Informatsii |
| **MASINT** | Izmeritelnaya i Signaturnaya Razvedka |
| **OSINT** | Razvedka Otkrytykh Istochnikov |
| **PIR** | Prioritetnoye Razvedyvatelnoye Trebovaniye |
| **SAT** | Strukturirovannaya Analiticheskaya Tekhnika |
| **SIGINT** | Signalnaya Razvedka |
| **TTP** | Taktiki, Tekhniki i Protsedury |

---

## Ssylki i Dopolnitelnoye Chteniye

### Ofitsialnyye Publikatsii
- Joint Publication 2-0: Joint Intelligence (US DoD)
- Intelligence Community Directive 203: Analytic Standards
- NIST SP 800-150: Guide to Cyber Threat Information Sharing

### Fundamentalnyye Teksty
- Heuer, R. (1999). *Psychology of Intelligence Analysis*
- Lowenthal, M. (2019). *Intelligence: From Secrets to Policy*
- Clark, R. (2019). *Intelligence Analysis: A Target-Centric Approach*

### Resursy CTI
- MITRE ATT&CK Framework: https://attack.mitre.org
- Bianco, D. (2013). *The Pyramid of Pain*: https://detect-respond.blogspot.com/2013/03/the-pyramid-of-pain.html
- STIX/TAXII Standards: https://oasis-open.github.io/cti-documentation/
- FIRST Traffic Light Protocol: https://www.first.org/tlp/

---

*Eta statya yavlyaetsya chastyu serii Osnovy Razvedki. Seriya napravlena na preodoleniye razryva mezhdu traditsionnym razvedyvatelnym remeslom i sovremennymi operatsiyami kiberrazvedki.*
