---
title: Kurz – pochopení kryptografie a certifikátů X. 509 pro Azure IoT Hub | Microsoft Docs
description: Kurz – pochopení kryptografie a X. 509 PKI pro Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 2c375a02f534572826e1ebd6b8549e59f6e83640
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378376"
---
# <a name="tutorial-understanding-public-key-cryptography-and-x509-public-key-infrastructure"></a>Kurz: porozumění kryptografii s veřejným klíčem a infrastruktuře veřejných klíčů X. 509

K ověřování zařízení v IoT Hub Azure můžete použít certifikáty X. 509. Certifikát je digitální dokument, který obsahuje veřejný klíč zařízení a který se dá použít k ověření, jestli zařízení splňuje požadavky. Certifikáty X. 509 a seznamy odvolaných certifikátů (CRL) jsou zdokumentovány v [dokumentu RFC 5280](https://tools.ietf.org/html/rfc5280). Certifikáty jsou jenom jednou ze součástí infrastruktury veřejných klíčů X. 509 (PKI). Pro pochopení infrastruktury PKI X. 509 je potřeba pochopit kryptografické algoritmy, kryptografické klíče, certifikáty a certifikační autority (CA):

* **Algoritmy** definují, jak se původní data v prostém textu transformují do šifrovaného textu a zpátky na prostý text.
* **Klíče** jsou náhodné nebo pseudonáhodných datové řetězce použité jako vstup do algoritmu.
* **Certifikáty** jsou digitální dokumenty, které obsahují veřejný klíč entity, a umožňují vám určit, jestli je předmět certifikátu, na koho se deklarace vztahuje.
* **Certifikační autority** potvrzují pravost předmětu certifikátu.

Certifikát si můžete koupit od certifikační autority (CA). Můžete také pro účely testování a vývoje nebo při práci v samostatném prostředí vytvořit kořenovou certifikační autoritu, která se podepisuje svým držitelem. Pokud například vlastníte jedno nebo více zařízení a otestujete ověřování pomocí služby IoT Hub, můžete svoji kořenovou certifikační autoritu podepsat svým držitelem a použít ji k vydávání certifikátů zařízení. Můžete také vydat certifikáty zařízení podepsané svým držitelem. Tento popis je popsaný v následujících článcích.

Než se podíváte na certifikáty X. 509 podrobněji a pomocí nich ověříte zařízení na IoT Hub, probereme kryptografii, na které jsou certifikáty založené.

## <a name="cryptography"></a>Kryptografie

Kryptografie se používá k ochraně informací a komunikací. To se obvykle provádí pomocí kryptografických technik pro kódování prostého textu (běžný text) do šifrovaného textu (kódovaný text) a zpátky znovu. Tento proces kódování se nazývá šifrování. Proces zpětného zpracování se nazývá dešifrování. Kryptografie se týká následujících cílů:

* **Důvěrnost**: informace může pochopit jenom zamýšlená cílová skupina.
* **Integrita**: informace nelze měnit v úložišti ani při přenosu.
* **Neodmítnutí**: Tvůrce informací nemůže později odepřít toto vytvoření.
* **Ověřování**: odesílatel a příjemce si můžou ověřit identitu každé druhé.

## <a name="encryption"></a>Šifrování

Proces šifrování vyžaduje algoritmus a klíč. Algoritmus definuje, jak se data transformují z prostého textu do šifrovaného textu do šifrovaného textu a zpátky do prostého textu. Klíč je náhodný řetězec dat, který se používá jako vstup do algoritmu. Všechna zabezpečení procesu jsou obsažena v klíči. Klíč musí být proto bezpečně uložen. Podrobnosti nejoblíbenějších algoritmů jsou ale veřejně dostupné.

Existují dva typy šifrování. Symetrické šifrování používá pro šifrování i dešifrování stejný klíč. Asymetrické šifrování používá pro šifrování a dešifrování jiné, ale matematické klíče.

### <a name="symmetric-encryption"></a>Symetrické šifrování

Symetrické šifrování používá stejný klíč k šifrování prostého textu do šifrovaného textu a dešifrování šifrovaného textu zpět do prostého textu. Potřebná délka klíče vyjádřená v počtu bitů je určena algoritmem. Po použití klíče k šifrování prostého textu se zašifrovaná zpráva pošle příjemci, který pak dešifrovaný text dešifruje. Symetrický klíč musí být zabezpečeně přenesen příjemci. Odeslání klíče představuje největší bezpečnostní riziko při použití symetrického algoritmu.

![Příklad symetrického šifrování](media/tutorial-x509-introduction/symmetric-keys.png)

### <a name="asymmetric-encryption"></a>Asymetrické šifrování

Pokud se používá jenom symetrické šifrování, je problém tím, že všechny strany této komunikace musí mít privátní klíč. Je však možné, že během přenosu do autorizovaných uživatelů může klíč zachytit neoprávněné třetí strany. Pro vyřešení tohoto problému použijte místo toho kryptografii asymetrického nebo veřejného klíče.

V asymetrické kryptografii má každý uživatel dva matematické klíče s názvem pár klíčů. Jeden klíč je veřejný a druhý klíč je privátní. Pár klíčů zajišťuje, že přístup k privátnímu klíči potřebnému k dešifrování dat má pouze příjemce. Následující obrázek shrnuje asymetrický proces šifrování.

![Příklad asymetrického šifrování](media/tutorial-x509-introduction/asymmetric-keys.png)

1. Příjemce vytvoří pár veřejného klíče a pošle veřejný klíč certifikační autoritě. Certifikační autorita zabalí veřejný klíč do certifikátu X. 509.

1. Odesílající strana získá veřejný klíč příjemce od certifikační autority.

1. Odesílatel šifruje data ve formátu prostého textu pomocí šifrovacího algoritmu. Veřejný klíč příjemce slouží k šifrování.

1. Odesílatel přenáší šifrovaný text příjemci. Není nutné klíč odeslat, protože příjemce už má privátní klíč potřebný k dešifrování šifrovaného textu.

1. Příjemce dešifruje šifrovaný text pomocí zadaného asymetrického algoritmu a privátního klíče.

### <a name="combining-symmetric-and-asymmetric-encryption"></a>Kombinování symetrického a asymetrického šifrování

Symetrické a asymetrické šifrování lze kombinovat, aby bylo možné využít jejich relativní síly. Symetrické šifrování je mnohem rychlejší než asymetrické, ale kvůli nutnosti posílání privátních klíčů jiným stranám není tak bezpečné. Ke spojování dvou typů lze použít symetrické šifrování pro převod prostého textu na šifrovaný text. Pro výměnu symetrického klíče se používá asymetrické šifrování. To je znázorněno v následujícím diagramu.

![Symetrické a assymetric šifrování](media/tutorial-x509-introduction/symmetric-asymmetric-encryption.png)

1. Odesílatel načte veřejný klíč příjemce.

1. Odesilatel vygeneruje symetrický klíč a použije ho k zašifrování původních dat.

1. Odesílatel používá veřejný klíč příjemce k šifrování symetrického klíče.

1. Odesílatel přenáší zašifrovaný symetrický klíč a šifrovaný text na zamýšleného příjemce.

1. Příjemce používá privátní klíč, který se shoduje s veřejným klíčem příjemce k dešifrování symetrického klíče odesílatele.

1. Příjemce používá symetrický klíč k dešifrování šifrovaného textu.

### <a name="asymmetric-signing"></a>Asymetrické podepisování

Asymetrické algoritmy lze použít k ochraně dat před změnou a k důkazu identity tvůrce dat. Následující obrázek ukazuje, jak asymetrické podepisování pomáhá prokázat identitu odesilatele.

![Příklad asymetrického podepsání](media/tutorial-x509-introduction/asymmetric-signing.png)

1. Odesílatel předává data ve formátu prostého textu pomocí asymetrického šifrovacího algoritmu, který používá privátní klíč k šifrování. Všimněte si, že tento scénář vrátí použití privátních a veřejných klíčů popsaných v předchozí části, které podrobně popisuje asymetrické šifrování.

1. Výsledný šifrovaný text se pošle příjemci.

1. Příjemce získá veřejný klíč původce z adresáře.

1. Příjemce dešifruje šifrovaný text pomocí veřejného klíče původce. Výsledný prostý text prokáže identitu původce, protože pouze původce má přístup k privátnímu klíči, který původně zašifroval původní text.

## <a name="signing"></a>certifikát

Digitální podpis lze použít k určení, zda byla data upravena při přenosu nebo v klidovém režimu. Data se předávají pomocí algoritmu hash, což je jednosměrná funkce, která vytváří matematický výsledek z dané zprávy. Výsledek se nazývá *hodnota hash*, *výtah zprávy*, *algoritmus Digest*, *podpis* nebo *kryptografický otisk*. Hodnotu hash nelze vrátit zpět, aby získala původní zprávu. Vzhledem k tomu, že malá změna ve zprávě má za následek významnou změnu v *kryptografickém otisku*, je možné použít hodnotu hash k určení, zda byla zpráva změněna. Následující obrázek ukazuje, jak se dají použít asymetrické šifrování a algoritmy hash k ověření, že se zpráva nezměnila.

![Příklad podpisu](media/tutorial-x509-introduction/signing.png)

1. Odesilatel vytvoří zprávu ve formátu prostého textu.

1. Odesílatel vyhodnotí hodnotu hash zprávy ve formátu prostého textu, aby vytvořil výtah zprávy.

1. Odesílatel šifruje výtah pomocí privátního klíče.

1. Odesílatel přenáší zprávu ve formátu prostého textu a šifrovaný výtah na zamýšleného příjemce.

1. Příjemce dešifruje výtah pomocí veřejného klíče odesílatele.

1. Příjemce spustí stejný algoritmus hash, který použil odesílatel ve zprávě.

1. Příjemce porovná výsledný podpis s dešifrovaným podpisem. Pokud jsou hodnoty Digest stejné, zpráva během přenosu se nezměnila.

## <a name="next-steps"></a>Další kroky

Další informace o polích, které tvoří certifikát, najdete v tématu [Principy certifikátů veřejných klíčů X. 509](tutorial-x509-certificates.md).

Pokud už znáte spoustu certifikátů X. 509 a chcete vygenerovat testovací verze, které můžete použít k ověření IoT Hub, přečtěte si následující témata:

* [Vytváření testovacích certifikátů pomocí skriptů Microsoft-Supplied](tutorial-x509-scripts.md)
* [Vytváření testovacích certifikátů pomocí OpenSSL](tutorial-x509-openssl.md)
* [Vytváření Self-Signed testovacích certifikátů pomocí OpenSSL](tutorial-x509-self-sign.md)

Pokud máte certifikát certifikační autority (CA) nebo certifikát podřízené certifikační autority a chcete ho nahrát do služby IoT Hub a prověřit si, že ho vlastníte, přečtěte si téma [prokazující vlastnictví certifikátu certifikační autority](tutorial-x509-prove-possession.md).
