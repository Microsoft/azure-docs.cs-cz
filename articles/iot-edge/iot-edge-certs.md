---
title: Certifikáty pro zabezpečení zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Azure IoT Edge používá certifikát k ověření zařízení, moduly a zařízení typu list a navázat zabezpečené připojení mezi nimi.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9e4fd0203d68ef1f39d6efbb9d17d3e517969bff
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457274"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Vysvětlení způsobu, jakým Azure IoT Edge používá certifikáty

K ověření identity a legitimity modulu runtime [centra IoT Edge](iot-edge-runtime.md#iot-edge-hub) , ke kterému se připojují, se IoT Edge certifikáty používají pro moduly a podřízená zařízení IoT. Tyto ověřování povolit protokol TLS (zabezpečení transportní vrstvy) zabezpečené připojení mezi modul runtime, moduly a zařízení IoT. Jako služby IoT Hub, sama IoT Edge vyžaduje zabezpečené a šifrované připojení z IoT směru server-klient (listy) zařízení a moduly IoT Edge. Aby bylo možné vytvořit zabezpečené připojení TLS, modul IoT Edge hub prezentuje řetěz certifikátů serveru pro připojení klientů, aby ověřil jeho identitu.

Tento článek vysvětluje, jak IoT Edge certifikáty můžou fungovat v produkčních, vývojových a testovacích scénářích. Tyto skripty jsou různé (prostředí Powershell nebo bash), jsou pojmy stejné mezi systémy Linux a Windows.

## <a name="iot-edge-certificates"></a>Certifikáty IoT Edge

Výrobci nejsou obvykle koncoví uživatelé zařízení IoT Edge. V některých případech je jediným vztahem mezi těmito dvěma pokaždé, když koncový uživatel, nebo operátor zakoupí obecné zařízení provedené výrobcem. Jindy, výrobce v rámci smlouvy pracuje na sestavení vlastního zařízení jménem operátora. Návrh certifikát IoT Edge se pokusí vezměte v úvahu oba scénáře.

Následující obrázek znázorňuje použití IoT Edge certifikátů. Mezi certifikátem kořenové certifikační autority a certifikátem certifikační autority zařízení může být nulový, jeden nebo mnoho zprostředkujících podpisových certifikátů v závislosti na počtu zúčastněných entit. Tady zobrazujeme jeden případ.

![Diagram vztahů typické certifikátu](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Certifikační autorita

Certifikační autority, nebo "CA" pro krátkodobé, je entita, která vystavuje digitální certifikáty. Certifikační autorita slouží jako důvěryhodné třetích stran mezi vlastníka a příjemce certifikátu. Digitální certifikát certifikuje vlastnictví příjemce certifikátu veřejného klíče. Řetěz certifikátů důvěryhodnosti funguje tak, že původně vydávání kořenový certifikát, který slouží jako základ pro vztah důvěryhodnosti v všechny certifikáty vydané autoritou. Vlastník později, můžete použít kořenový certifikát vydat další zprostředkujících certifikátů ("listy").

### <a name="root-ca-certificate"></a>Certifikát kořenové certifikační Autority

Certifikát kořenové certifikační Autority je kořenové důvěryhodnosti celý proces. V produkčních scénářích se tento certifikát certifikační autority obvykle kupuje od důvěryhodné Komerční certifikační autority, jako je Baltimore, VeriSign nebo DigiCert. Pokud máte úplnou kontrolu nad zařízeními, která se připojují k zařízením IoT Edge, je možné použít certifikační autoritu na úrovni podniku. V obou případech se v rámci centra IoT Edge nahoru zobrazí celý řetěz certifikátů, takže zařízení IoT na listech musí důvěřovat kořenovému certifikátu. Certifikát kořenové certifikační autority můžete uložit buď do úložiště Důvěryhodné kořenové certifikační autority, nebo zadat podrobnosti certifikátu v kódu aplikace.

### <a name="intermediate-certificates"></a>Zprostředkující certifikáty

V typické výrobní proces pro vytváření zabezpečených zařízení certifikáty kořenové certifikační Autority jsou zřídka se používá přímo, hlavně kvůli riziku úniku nebo odhalení. Certifikát kořenové certifikační autority vytvoří a digitálně podepíše jeden nebo víc certifikátů Zprostředkující certifikační autority. Může existovat pouze jeden, nebo může být řetěz tyto zprostředkující certifikáty. Scénáře, které by vyžadovaly řetěz zprostředkující certifikáty jsou tyto:

* Hierarchie oddělení v rámci výrobce.

* Zahrnuté sériově v produkčním prostředí zařízení více společností.

* Zákazník nákupu kořenové certifikační Autority a odvozování podpisový certifikát pro výrobce pro přihlášení zařízení, která využívají jménem daného zákazníka.

Výrobce v každém případě používá k podepisování certifikátu certifikační Autority zařízení umístí na zařízení end certifikátem zprostředkující certifikační Autority na konci tohoto řetězce. Obecně tyto zprostředkující certifikáty jsou ve výrobním závodě přísně chráněný. Jejich procházejí přesně vymezené procesy fyzického a elektronické pro jejich použití.

### <a name="device-ca-certificate"></a>Certifikát certifikační Autority zařízení

Certifikát certifikační Autority zařízení je generují z a podepíše konečné certifikát zprostředkující certifikační Autority v procesu. Tento certifikát se instaluje do samotného IoT Edge zařízení, nejlépe v zabezpečeném úložišti, jako je modul hardwarového zabezpečení (HSM). Kromě toho certifikátu certifikační Autority zařízení jednoznačně identifikuje zařízení IoT Edge. Certifikát certifikační autority zařízení může podepsat jiné certifikáty. 

### <a name="iot-edge-workload-ca"></a>Úlohy IoT Edge certifikační Autority

[IoT Edge Security Manager](iot-edge-security-manager.md) generuje certifikát certifikační autority pro úlohy, první na straně operátora procesu, při IoT Edge prvním spuštění. Tento certifikát je vygenerovaný z a podepsaný certifikátem"zařízení certifikační Autority". Tento certifikát, který je právě jinou zprostředkující podpisový certifikát, se používá pro generování a podepisování jiných certifikátů, používá modul runtime IoT Edge. V dnešní době je to hlavně certifikát serveru IoT Edge hub, který je popsaný v následující části, ale v budoucnu může zahrnovat další certifikáty pro ověřování IoT Edgech součástí.

### <a name="iot-edge-hub-server-certificate"></a>Certifikát serveru IoT Edge hub

Certifikát serveru IoT Edge hub je skutečný certifikát prezentovaný koncovým zařízením a modulům pro ověření identity během navazování připojení TLS, které vyžaduje IoT Edge. Tento certifikát představuje úplný řetěz podpisových certifikátů sloužící ke generování až kořenový certifikát certifikační Autority, která musí zařízení IoT typu list důvěřovat. Při vygenerování správcem zabezpečení IoT Edge se běžný název (CN) tohoto certifikátu IoT Edge hub nastaví na vlastnost hostname v souboru config. yaml po převodu na malý případ. To je běžné příčiny záměně s IoT Edge.

## <a name="production-implications"></a>Dopad na produkční

Může být rozumné otázku "Proč IoT Edge potřebuje"zatížení certifikační Autority"navíc certifikát? Nepovedlo se nám použít certifikát pro certifikační autoritu zařízení k přímému vygenerování certifikátu serveru IoT Edge hub? ". Technicky může. Účelem tento zprostředkující certifikát "úloha" je však k oddělení obavy mezi výrobce zařízení a zařízení – operátor. Představte si situaci, kdy je zařízení IoT Edge prodává nebo přenést z jednoho zákazníka na jiný. Pravděpodobně budete potřebovat certifikát certifikační Autority zařízení dodané výrobcem, chcete-li být neměnitelný. "Úloha" certifikáty specifické pro funkce zařízení však by měl vymazat a znovu vytvořit pro nové nasazení.

Vzhledem k tomu, že jsou výrobní a provozní procesy oddělené, zvažte následující důsledky při přípravě produkčních zařízení:

* S jakýkoli proces na základě certifikátů certifikát kořenové certifikační Autority a všechny zprostředkující certifikáty certifikační Autority by měly budou zabezpečené a monitorované během celého procesu zavádění zařízení IoT Edge. Výrobce zařízení IoT Edge by měl mít silné procesy správné úložiště a používání jejich zprostředkující certifikáty. Kromě toho je třeba zařízení certifikát certifikační Autority uchovávat v jako zabezpečené úložiště jako na samotných zařízeních nejlépe modulu hardwarového zabezpečení.

* Certifikát serveru IoT Edge hub je prezentován IoT Edgem rozbočovačem a připojenými klientskými zařízeními a moduly. Běžný název (CN) certifikátu certifikační autority zařízení **nesmí být** stejný jako název hostitele, který se použije v souboru config. yaml na zařízení IoT Edge. Název používaný klienty pro připojení k IoT Edge (například prostřednictvím parametru GatewayHostName připojovacího řetězce nebo příkazu připojit v MQTT) **nemůže být** stejný jako běžný název, který se používá v certifikátu certifikační autority zařízení. Toto omezení je způsobeno tím, že centrum IoT Edge prezentuje celý řetěz certifikátů pro ověřování klienty. Pokud má certifikát serveru IoT Edge hub a certifikát certifikační autority pro zařízení stejný CN, dostanete se do ověřovací smyčky a certifikát se zruší.

* Vzhledem k tomu, že je certifikát CA zařízení používán démonem zabezpečení IoT Edge k vygenerování konečných certifikátů IoT Edge, musí se jednat o podpisový certifikát, což znamená, že má funkce podepisování certifikátů. Použití certifikační autority V3 Basic Constraints: true pro certifikát certifikační autority zařízení automaticky nastaví požadované vlastnosti použití klíče.

>[!Tip]
> Pokud jste jste už prošly přes nastavení služby IoT Edge jako transparentní brána ve scénáři pro vývoj/testování, pomocí našich "pohodlí skriptů" (viz další část) a používá se stejným názvem hostitele při vytváření certifikátu zařízení certifikační Autority, jako jste to udělali u názvu hostitele v config.yaml , budete vás možná zajímat, proč to šlo. Ve snaze ke zjednodušení prostředí pro vývojáře skriptů pohodlí připojí ".ca" na konci názvu, které můžete předat do skriptu. Ano, například pokud jste pro obě názvu zařízení ve skriptech a název hostitele v config.yaml "mygateway", nejprve bude převeden na mygateway.ca před používá jako CN certifikátu zařízení certifikační Autority.

## <a name="devtest-implications"></a>Důsledky pro vývoj/testování

Pro vývoj a testování scénářů, společnost Microsoft poskytuje sadu [pohodlí skripty](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) pro generování-li se o neprodukční certifikátů vhodných pro IoT Edge ve scénáři transparentní brány. Příklady fungování skriptů najdete v tématu [Vytvoření ukázkových certifikátů pro otestování IoT Edgech funkcí zařízení](how-to-create-test-certificates.md).

>[!Tip]
> Připojit zařízení IoT "typu list" zařízení a aplikací, které používají naše sada SDK zařízení IoT pomocí IoT Edge, je nutné přidat volitelný parametr GatewayHostName ke konci připojovací řetězec zařízení. Při generování certifikátu serveru Edge Hub je založen na verzi nižší malými a velkými písmeny názvu hostitele z config.yaml, proto pro názvy shody a ověřovací certifikát TLS proběhla úspěšně, měli byste zadat parametr GatewayHostName malými písmeny.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Příklad hierarchie certifikačních autorit IoT Edge

K ilustraci této cestě k certifikátu, je na následujícím snímku obrazovky ze zařízení IoT Edge nastavit jako transparentní brána funkční. OpenSSL se používá pro připojení k centru IoT Edge, ověřování a vystavování certifikátů.

![Snímek obrazovky hierarchii certifikátů na všech úrovních](./media/iot-edge-certs/iotedge-cert-chain.png)

Zobrazí se hierarchie certifikátů hloubky reprezentované na snímku obrazovky:

| Certifikát kořenové certifikační Autority         | Azure IoT Hub CA certifikátu pouze Test                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Certifikát zprostředkující certifikační Autority | Azure IoT Hub zprostředkující certifikát pouze Test                                                                 |
| Certifikát certifikační Autority zařízení       | iotgateway.CA ("iotgateway" byla předána jako < brány název hostitele > pohodlí skripty)      |
| Certifikát pracovního vytížení certifikační Autority     | certifikační autorita iotedge pracovního vytížení                                                                                       |
| Certifikát serveru IoT Edge hub | iotedgegw.Local (shoduje s názvem "hostitele" z config.yaml)                                                |

## <a name="next-steps"></a>Další kroky

[Vysvětlení modulů Azure IoT Edge](iot-edge-modules.md)

[Konfigurace zařízení IoT Edge tak, aby fungovalo jako transparentní brána](how-to-create-transparent-gateway.md)