---
title: Certifikáty pro zabezpečení zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Azure IoT Edge používá certifikát k ověření zařízení, moduly a zařízení typu list a navázat zabezpečené připojení mezi nimi.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 09/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1cf1411e227363e7dc9d54f04d0c630341f55a6e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099877"
---
# <a name="azure-iot-edge-certificate-usage-detail"></a>Podrobnosti o použití certifikátu Azure IoT Edge

IoT Edge certifikáty se používají pro příjem dat zařízení IoT a modulů k ověření identity a legitimitu [centrum Edge](iot-edge-runtime.md#iot-edge-hub) modul runtime, kterému se připojuje. Tyto ověřování povolit protokol TLS (zabezpečení transportní vrstvy) zabezpečené připojení mezi modul runtime, moduly a zařízení IoT. Jako služby IoT Hub, sama IoT Edge vyžaduje zabezpečené a šifrované připojení z IoT směru server-klient (listy) zařízení a moduly IoT Edge. Vytvořit zabezpečené připojení TLS, Centrum Edge modul představuje server řetězec certifikátů pro připojení klientů mohl ověřit jeho identitu.

Vymazat certifikátů záměny o použití IoT Edge, tento článek vysvětluje, jak pracovat IoT Edge certifikáty ve scénářích ve velkém měřítku, jakož i vývojové a testovací scénáře. Tyto skripty jsou různé (prostředí Powershell nebo bash), jsou pojmy stejné mezi systémy Linux a Windows.

## <a name="iot-edge-certificates"></a>Certifikáty IoT Edge

Ve většině případů jsou výrobci nezávislé entity koncovým uživatelům hraničního zařízení. V některých případech pouze vztah mezi výrobce a operátor je koupit komerčně dostupný hraniční zařízení. Jindy výrobce práce v rámci smlouvy k vytváření hraničního zařízení jménem operátor. Návrh certifikát IoT Edge se pokusí vezměte v úvahu oba scénáře.

Následující obrázek znázorňuje použití IoT Edge certifikátů. Může být žádný, jeden nebo mnoho zprostředkující podpisové certifikáty mezi certifikát kořenové certifikační Autority a certifikát certifikační Autority zařízení. V závislosti na počtu účastnící se entity tady vám ukážeme, jeden případ.

![Diagram vztahů typické certifikátu](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Certifikační autorita

Certifikační autority, nebo "CA" pro krátkodobé, je entita, která vystavuje digitální certifikáty. Certifikační autorita slouží jako důvěryhodné třetích stran mezi vlastníka a příjemce certifikátu. Digitální certifikát certifikuje vlastnictví příjemce certifikátu veřejného klíče. Řetěz certifikátů důvěryhodnosti funguje tak, že původně vydávání kořenový certifikát, který slouží jako základ pro vztah důvěryhodnosti v všechny certifikáty vydané autoritou. Vlastník později, můžete použít kořenový certifikát vydat další zprostředkujících certifikátů ("listy").

### <a name="root-ca-certificate"></a>Certifikát kořenové certifikační Autority

Certifikát kořenové certifikační Autority je kořenové důvěryhodnosti celý proces. V produkčních scénářích to obvykle bude certifikát certifikační Autority zakoupenému od důvěryhodné komerční certifikační autority jako Baltimore, Verisign, DigiCert atd. By měl mít úplnou kontrolu nad zařízeními připojení pro vaše hraniční zařízení, je možné použít podnikové úrovni certifikační autority. V obou případech celý řetěz certifikátů z Centrum Edge se zobrazí souhrn, tak zařízení IoT typu list musí důvěřovat kořenovému certifikátu. Můžete ukládat buď v úložišti důvěryhodných kořenových certifikátů autorita certifikátu kořenové certifikační Autority, nebo zadat kořenové podrobnosti o certifikátu certifikační Autority v kódu při připojení klienta IoT na hraničních zařízeních IoT.

### <a name="intermediate-certificates"></a>Zprostředkující certifikáty

V typické výrobní proces pro vytváření zabezpečených zařízení certifikáty kořenové certifikační Autority jsou zřídka se používá přímo, hlavně kvůli riziku úniku nebo odhalení. Obecně platí nejméně jeden zprostředkující certifikáty certifikační Autority jsou vytvořené a digitálně podepíše certifikát kořenové certifikační Autority. Může existovat pouze jeden, nebo může být řetěz tyto zprostředkující certifikáty. Scénáře, které by vyžadovaly řetěz zprostředkující certifikáty jsou tyto:

* Hierarchie oddělení v rámci výrobce.

* Zahrnuté sériově v produkčním prostředí zařízení více společností.

* Zákazník nákupu kořenové certifikační Autority a odvozování podpisový certifikát pro výrobce pro přihlášení zařízení, která využívají jménem daného zákazníka.

Výrobce v každém případě používá k podepisování certifikátu certifikační Autority zařízení umístí na zařízení end certifikátem zprostředkující certifikační Autority na konci tohoto řetězce. Obecně tyto zprostředkující certifikáty jsou ve výrobním závodě přísně chráněný. Jejich procházejí přesně vymezené procesy fyzického a elektronické pro jejich použití.

### <a name="device-ca-certificate"></a>Certifikát certifikační Autority zařízení

Certifikát certifikační Autority zařízení je generují z a podepíše konečné certifikát zprostředkující certifikační Autority v procesu. Tento certifikát nainstalován na hraniční zařízení samostatně, pokud možno v zabezpečeném úložišti, jako je například modulu hardwarového zabezpečení (HSM). Kromě toho certifikátu certifikační Autority zařízení jednoznačně identifikuje zařízení IoT Edge. IoT Edge je schopen vystavování dalších certifikátů zařízení certifikát certifikační Autority. Například certifikát zařízení certifikační Autority vydá listové certifikáty zařízení, které se používají k ověřování zařízení a [Azure IoT Device Provisioning Service](../iot-dps/about-iot-dps.md).

### <a name="iot-edge-workload-ca"></a>Úlohy IoT Edge certifikační Autority

Tento certifikát, první na straně "operátor" procesu, vygeneruje [IoT Edge Security Manager](iot-edge-security-manager.md) při prvním spuštění IoT Edge. Tento certifikát je vygenerovaný z a podepsaný certifikátem"zařízení certifikační Autority". Tento certifikát, který je právě jinou zprostředkující podpisový certifikát, se používá pro generování a podepisování jiných certifikátů, používá modul runtime IoT Edge. V současné době, která je primárně Centrum Edge certifikát serveru popsaných v následující části, ale v budoucnu může obsahovat další certifikáty pro ověřování komponenty IoT Edge.

### <a name="edge-hub-server-certificate"></a>Certifikát serveru Edge Hub

Skutečné certifikátu uváděnému zařízení typu list a moduly pro ověření identity při navazování připojení TLS vyžadují IoT Edge je certifikát serveru Edge Hub. Tento certifikát představuje úplný řetěz podpisových certifikátů sloužící ke generování až kořenový certifikát certifikační Autority, která musí zařízení IoT typu list důvěřovat. Při generování IoT Edge zabezpečení správcem, běžný název (CN), toto centrum Edge certifikátu se nastaví na název poskytuje vlastnost "hostname" v souboru config.yaml po převodu na malá písmena. To je běžné příčiny záměně s IoT Edge.

## <a name="production-implications"></a>Dopad na produkční

Může být rozumné otázku "Proč IoT Edge potřebuje"zatížení certifikační Autority"navíc certifikát? Nelze ho použít zařízení certifikační Autority certifikát přímo vygenerovat certifikát serveru Edge Hub? ". Technicky může. Účelem tento zprostředkující certifikát "úloha" je však k oddělení obavy mezi výrobce zařízení a zařízení – operátor. Představte si situaci, kdy je zařízení IoT Edge prodává nebo přenést z jednoho zákazníka na jiný. Pravděpodobně budete potřebovat certifikát certifikační Autority zařízení dodané výrobcem, chcete-li být neměnitelný. "Úloha" certifikáty specifické pro funkce zařízení však by měl vymazat a znovu vytvořit pro nové nasazení.

Protože výrobce a operátor procesy jsou oddělené, existuje pár důsledky, které je potřeba považovat za související s zavádět IoT Edge pro zařízení s produkčním prostředí.

* S jakýkoli proces na základě certifikátů certifikát kořenové certifikační Autority a všechny zprostředkující certifikáty certifikační Autority by měly budou zabezpečené a monitorované během celého procesu zavádění zařízení IoT Edge. Výrobce zařízení IoT Edge by měl mít silné procesy správné úložiště a používání jejich zprostředkující certifikáty. Kromě toho je třeba zařízení certifikát certifikační Autority uchovávat v jako zabezpečené úložiště jako na samotných zařízeních nejlépe modulu hardwarového zabezpečení.

* Protože certifikát serveru Centrum Edge se zobrazí Edge hub připojení klientských zařízení a moduly, CN certifikát zařízení certifikační Autority **nesmí být** stejný jako "název hostitele", který se použije v config.yaml na hraničních zařízeních IoT zařízení. Název používají klienti pro připojení k IoT Edge (např. parametru GatewayHostName připojovací řetězec nebo příkaz připojit v MQTT) **nemůže být** stejný jako běžný název používaný v certifikátu zařízení certifikační Autority. Je to proto, že Centrum Edge se uvede jeho celý řetěz certifikátů pro ověřování klientů. Pokud certifikát serveru Edge Hub a certifikát zařízení certifikační Autority, že obě mají stejné CN, získáte ve smyčce ověření a zruší platnost certifikátu.

* Protože certifikát certifikační Autority zařízení používá ke generování certifikátů konečné IoT Edge démona zabezpečení IoT Edge, ho musí být sám podpisový certifikát, což znamená, že má funkce podepsání certifikátu. Chcete-li to podpisový certifikát certifikační Autority, v podstatě použití "V3 základních omezení CA:True" na certifikátu certifikační Autority zařízení automaticky nastaví vlastnosti vyžaduje použití klíče.

>[!Tip]
> Pokud jste jste už prošly přes nastavení služby IoT Edge jako transparentní brána ve scénáři pro vývoj/testování, pomocí našich "pohodlí skriptů" (viz další část) a používá se stejným názvem hostitele při vytváření certifikátu zařízení certifikační Autority, jako jste to udělali u názvu hostitele v config.yaml , budete vás možná zajímat, proč to šlo. Ve snaze ke zjednodušení prostředí pro vývojáře skriptů pohodlí připojí ".ca" na konci názvu, které můžete předat do skriptu. Ano, například pokud jste pro obě názvu zařízení ve skriptech a název hostitele v config.yaml "mygateway", nejprve bude převeden na mygateway.ca před používá jako CN certifikátu zařízení certifikační Autority.

## <a name="devtest-implications"></a>Důsledky pro vývoj/testování

Pro vývoj a testování scénářů, společnost Microsoft poskytuje sadu [pohodlí skripty](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) pro generování-li se o neprodukční certifikátů vhodných pro IoT Edge ve scénáři transparentní brány. Příklady, jak tyto skripty fungují, najdete v části [nakonfigurovat nastavení zařízení IoT Edge tak, aby fungoval jako transparentní brána](how-to-create-transparent-gateway.md).

Tyto skripty vygenerujete certifikáty, které vycházet ze struktury řetěz certifikátů popsaných v tomto článku. Následující příkazy Generovat "kořenový certifikát certifikační Autority" a jedné "certifikát zprostředkující certifikační Autority".

```bash
./certGen.sh create_root_and_intermediate 
```

```Powershell
New-CACertsCertChain rsa 
```

Tyto příkazy, generovat "Certifikační Autorita certifikátu zařízení".

```bash
./certGen.sh create_edge_device_certificate "<gateway device name>"
```

```Powershell
New-CACertsEdgeDevice "<gateway device name>"
```

* **\<Název zařízení brány\>** předán tyto skripty **nesmí** být stejný jako parametr "hostname" v config.yaml. Jak už bylo zmíněno, skripty vám pomoct vyhnout se problémy s připojením řetězec ".ca" **\<název zařízení brány\>** zabránit kolize názvů v případě, že uživatel nastaví na hraničních zařízeních pomocí stejného názvu v obou místech je však doporučujeme, abyste se stejným názvem.

>[!Tip]
> Připojit zařízení IoT "typu list" zařízení a aplikací, které používají naše sada SDK zařízení IoT pomocí IoT Edge, je nutné přidat volitelný parametr GatewayHostName ke konci připojovací řetězec zařízení. Při generování certifikátu serveru Edge Hub je založen na verzi nižší malými a velkými písmeny názvu hostitele z config.yaml, proto pro názvy shody a ověřovací certifikát TLS proběhla úspěšně, měli byste zadat parametr GatewayHostName malými písmeny.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Příklad hierarchie certifikačních autorit IoT Edge

K ilustraci této cestě k certifikátu, je na následujícím snímku obrazovky ze zařízení IoT Edge nastavit jako transparentní brána funkční. OpenSSL slouží k připojení k centru Edge, ověřit a vypsat si certifikáty.

![Snímek obrazovky hierarchii certifikátů na všech úrovních](./media/iot-edge-certs/iotedge-cert-chain.png)

Zobrazí se hierarchie certifikátů hloubky reprezentované na snímku obrazovky:

| Certifikát kořenové certifikační Autority         | Azure IoT Hub CA certifikátu pouze Test                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Certifikát zprostředkující certifikační Autority | Azure IoT Hub zprostředkující certifikát pouze Test                                                                 |
| Certifikát certifikační Autority zařízení       | iotgateway.CA ("iotgateway" byla předána jako < brány název hostitele > pohodlí skripty)      |
| Certifikát pracovního vytížení certifikační Autority     | certifikační autorita iotedge pracovního vytížení                                                                                       |
| Certifikát serveru Edge Hub | iotedgegw.Local (shoduje s názvem "hostitele" z config.yaml)                                                |

## <a name="next-steps"></a>Další postup

[Vysvětlení modulů Azure IoT Edge](iot-edge-modules.md)

[Konfigurace zařízení tak, aby fungoval jako transparentní brána IoT Edge](how-to-create-transparent-gateway.md)