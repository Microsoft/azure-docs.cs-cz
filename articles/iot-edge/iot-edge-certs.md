---
title: Certifikáty pro zabezpečení zařízení-Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge používá certifikát k ověření zařízení, modulů a listových zařízení a k vytvoření zabezpečených připojení mezi nimi.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: mqtt
ms.openlocfilehash: d1d4abbcc0768915d7d2e693cfc76a699ed21a91
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89669628"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Vysvětlení způsobu, jakým Azure IoT Edge používá certifikáty

IoT Edge certifikáty používají moduly a zařízení IoT pro příjem dat k ověření identity a legitimity modulu runtime [centra IoT Edge](iot-edge-runtime.md#iot-edge-hub) . Tato ověření umožňují zabezpečené připojení TLS (Transport Layer Security) mezi modulem runtime, moduly a zařízeními IoT. Stejně jako IoT Hub sám IoT Edge vyžaduje zabezpečené a šifrované připojení ze zařízení IoT (nebo list) a modulů IoT Edge. Aby bylo možné vytvořit zabezpečené připojení TLS, modul IoT Edge hub prezentuje řetěz certifikátů serveru pro připojení klientů, aby ověřil jeho identitu.

>[!NOTE]
>Tento článek se týká certifikátů, které se používají k zabezpečení připojení mezi různými součástmi na zařízení IoT Edge nebo mezi zařízením IoT Edge a všemi koncovými zařízeními. Pomocí certifikátů můžete také ověřit, že zařízení IoT Edge IoT Hub. Tyto ověřovací certifikáty se liší a nejsou popsány v tomto článku. Další informace o ověřování zařízení pomocí certifikátů najdete v tématu [Vytvoření a zřízení IoT Edge zařízení pomocí certifikátů X. 509](how-to-auto-provision-x509-certs.md).

Tento článek vysvětluje, jak IoT Edge certifikáty můžou fungovat v produkčních, vývojových a testovacích scénářích. I když se skripty liší (PowerShell vs. bash), koncepty jsou stejné mezi systémy Linux a Windows.

## <a name="iot-edge-certificates"></a>Certifikáty IoT Edge

Existují dva běžné scénáře nastavení certifikátů na zařízení IoT Edge. V některých případech může koncový uživatel (nebo operátor) zařízení nakoupit obecné zařízení, které provedl výrobce, a pak tyto certifikáty spravuje sami. Jinak výrobce pracuje pod smlouvou o sestavení vlastního zařízení pro operátor a provede některé počáteční podepisování certifikátu před tím, než se zařízení dokončí. IoT Edge návrh certifikátu se snaží vzít v úvahu oba scénáře.

Následující obrázek ukazuje IoT Edge využití certifikátů. Mezi certifikátem kořenové certifikační autority a certifikátem certifikační autority zařízení může být nulový, jeden nebo mnoho zprostředkujících podpisových certifikátů v závislosti na počtu zúčastněných entit. Tady zobrazujeme jeden případ.

![Diagram typických vztahů certifikátů](./media/iot-edge-certs/edgeCerts-general.png)

> [!NOTE]
> V současné době omezení libiothsm brání použití certifikátů, jejichž platnost vyprší, od 1. ledna 2038. Toto omezení se vztahuje na certifikát certifikační autority zařízení, všechny certifikáty v sadě prostředků trustu a na certifikáty ID zařízení, které se používají pro metody zřizování X. 509.

### <a name="certificate-authority"></a>Certifikační autorita

Certifikační autorita (CA) je krátká, jedná se o entitu, která vydává digitální certifikáty. Certifikační autorita funguje jako důvěryhodná třetí strana mezi vlastníkem a příjemcem certifikátu. Digitální certifikát osvědčí vlastnictví veřejného klíče příjemcem certifikátu. Řetěz certifikátů důvěry funguje tak, že nejprve vydává kořenový certifikát, který je základem pro důvěryhodnost ve všech certifikátech vydaných autoritou. Následně může vlastník použít kořenový certifikát k vystavení dalších zprostředkujících certifikátů (' list ' Certificate).

### <a name="root-ca-certificate"></a>Certifikát kořenové certifikační autority

Certifikát kořenové certifikační autority je kořenem vztahu důvěryhodnosti celého procesu. V produkčních scénářích se tento certifikát certifikační autority obvykle kupuje od důvěryhodné Komerční certifikační autority, jako je Baltimore, VeriSign nebo DigiCert. Pokud máte úplnou kontrolu nad zařízeními, která se připojují k zařízením IoT Edge, je možné použít certifikační autoritu na úrovni podniku. V obou případech se do něj zavede celý řetěz certifikátů z centra IoT Edge, aby se na listovém zařízení IoT musely důvěřovat kořenovému certifikátu. Certifikát kořenové certifikační autority můžete uložit buď do úložiště Důvěryhodné kořenové certifikační autority, nebo zadat podrobnosti certifikátu v kódu aplikace.

### <a name="intermediate-certificates"></a>Zprostředkující certifikáty

V typickém výrobním procesu pro vytváření zabezpečených zařízení se certifikáty kořenové certifikační autority používají jenom zřídka, hlavně kvůli riziku úniku nebo ozáření. Certifikát kořenové certifikační autority vytvoří a digitálně podepíše jeden nebo víc certifikátů Zprostředkující certifikační autority. Může existovat jenom jeden, nebo se může jednat o řetěz zprostředkujících certifikátů. Mezi scénáře, které by vyžadovaly řetěz zprostředkujících certifikátů, patří:

* Hierarchie oddělení v rámci výrobce.

* V produkčním prostředí se v produkci zařízení zapojí více společností.

* Zákazník, který kupuje kořenovou certifikační autoritu a vyvozuje podpisový certifikát pro výrobce k podepisování zařízení, která provedou jménem daného zákazníka.

V každém případě výrobce používá na konci tohoto řetězu certifikát zprostředkující certifikační autority k podepsání certifikátu certifikační autority zařízení, který je umístěný na koncovém zařízení. Obecně jsou tyto zprostředkující certifikáty pečlivě chráněné v výrobním závodě. Při jejich používání jsou náročné na fyzické i elektronické.

### <a name="device-ca-certificate"></a>Certifikát certifikační autority zařízení

Certifikát certifikační autority zařízení se vygeneruje a podepíše koncovým certifikátem zprostředkující certifikační autority v procesu. Tento certifikát se instaluje do samotného IoT Edge zařízení, nejlépe v zabezpečeném úložišti, jako je modul hardwarového zabezpečení (HSM). Certifikát certifikační autority zařízení navíc jednoznačně identifikuje zařízení IoT Edge. Certifikát certifikační autority zařízení může podepsat jiné certifikáty.

### <a name="iot-edge-workload-ca"></a>IoT Edge CA pro úlohy

[IoT Edge Security Manager](iot-edge-security-manager.md) generuje certifikát certifikační autority pro úlohy, první na straně operátora procesu, při IoT Edge prvním spuštění. Tento certifikát je vygenerovaný a podepsaný certifikátem certifikační autority zařízení. Tento certifikát, který je pouze dalším zprostředkujícím podpisovým certifikátem, se používá ke generování a podepsání všech dalších certifikátů používaných modulem runtime IoT Edge. V dnešní době je to hlavně certifikát serveru IoT Edge hub, který je popsaný v následující části, ale v budoucnu může zahrnovat další certifikáty pro ověřování IoT Edgech součástí.

### <a name="iot-edge-hub-server-certificate"></a>Certifikát serveru IoT Edge hub

Certifikát serveru IoT Edge hub je skutečný certifikát prezentovaný koncovým zařízením a modulům pro ověření identity během navazování připojení TLS, které vyžaduje IoT Edge. Tento certifikát prezentuje úplný řetěz podpisových certifikátů, které se používají ke generování, do certifikátu kořenové certifikační autority, které musí mít na listovém zařízení IoT důvěru. Při vygenerování správcem zabezpečení IoT Edge se běžný název (CN) tohoto certifikátu IoT Edge hub nastaví na vlastnost hostname v souboru config. yaml po převodu na malý případ. Tato konfigurace je běžnou zdrojem nejasností s IoT Edge.

## <a name="production-implications"></a>Vliv na produkci

Může to být důvod, proč IoT Edge potřebovat dodatečný certifikát certifikační autority pro úlohy? Nepovedlo se nám použít certifikát pro certifikační autoritu zařízení k přímému vygenerování certifikátu serveru IoT Edge hub? ". Technicky, může. Účelem tohoto "zprostředkujícího" certifikátu ale je, že se mezi výrobcem zařízení a operátorem zařízení oddělují obavy. Představte si situaci, kdy se zařízení IoT Edge prodává nebo přenáší z jednoho zákazníka do jiného. Pravděpodobně budete chtít, aby certifikát certifikační autority zařízení poskytnutý výrobcem byl neměnný. Pro nové nasazení se ale musí vymazat i certifikáty úlohy, které jsou specifické pro provoz zařízení.

Vzhledem k tomu, že jsou výrobní a provozní procesy oddělené, zvažte následující důsledky při přípravě produkčních zařízení:

* Při jakémkoli procesu založeném na certifikátu by se měl certifikát od kořenové certifikační autority a všechny certifikáty zprostředkující certifikační autority zabezpečit a monitorovat během celého procesu zavádění IoT Edge zařízení. Výrobce zařízení IoT Edge by měl mít k dispozici silné procesy pro správné uložení a využití jejich zprostředkujících certifikátů. Kromě toho by měl být certifikát certifikační autority zařízení uložený v zabezpečeném úložišti, a to v samotném zařízení, nejlépe v modulu hardwarového zabezpečení.

* Certifikát serveru IoT Edge hub je prezentován IoT Edgem rozbočovačem a připojenými klientskými zařízeními a moduly. Běžný název (CN) certifikátu certifikační autority zařízení **nesmí být** stejný jako název hostitele, který se použije v souboru config. yaml na zařízení IoT Edge. Název používaný klienty pro připojení k IoT Edge (například prostřednictvím parametru GatewayHostName připojovacího řetězce nebo příkazu připojit v MQTT) **nemůže být** stejný jako běžný název použitý v certifikátu certifikační autority zařízení. Toto omezení je způsobeno tím, že centrum IoT Edge prezentuje celý řetěz certifikátů pro ověřování klienty. Pokud má certifikát serveru IoT Edge hub a certifikát certifikační autority pro zařízení stejný CN, dostanete se do ověřovací smyčky a certifikát se zruší.

* Vzhledem k tomu, že je certifikát CA zařízení používán démonem zabezpečení IoT Edge k vygenerování konečných certifikátů IoT Edge, musí se jednat o podpisový certifikát, což znamená, že má funkce podepisování certifikátů. Použití certifikační autority V3 Basic Constraints: true pro certifikát certifikační autority zařízení automaticky nastaví požadované vlastnosti použití klíče.

>[!Tip]
> Pokud jste už prošli nastavením IoT Edge jako transparentní bránu ve scénáři pro vývoj/testování pomocí našich "pohodlných skriptů" (viz další oddíl) a při vytváření certifikátu certifikační autority pro zařízení jako hostitele v souboru config. yaml jste použili stejný název hostitele, může vás zajímat, proč pracoval. Aby se zjednodušilo prostředí pro vývojáře, snadno skripty připojí ". ca" na konci názvu, který do skriptu předáte. Pokud jste například použili "mygateway" pro název vašeho zařízení ve skriptech a v názvu hostitele v souboru config. yaml, dříve se před použitím jako CN pro certifikát certifikační autority pro zařízení zapnula na mygateway.ca.

## <a name="devtest-implications"></a>Důsledky pro vývoj a testování

Pro usnadnění vývojových a testovacích scénářů poskytuje společnost Microsoft sadu [praktických skriptů](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) pro generování neprodukčních certifikátů vhodných pro IoT Edge ve scénáři transparentní brány. Příklady fungování skriptů najdete v tématu [Vytvoření ukázkových certifikátů pro otestování IoT Edgech funkcí zařízení](how-to-create-test-certificates.md).

>[!Tip]
> Pokud chcete připojit zařízení IoT "list" a aplikace, které používají naši sadu SDK pro zařízení IoT prostřednictvím IoT Edge, musíte na konec připojovacího řetězce zařízení přidat volitelný parametr GatewayHostName. Když se vygeneruje certifikát serveru Edge hub, vychází z použita verze hostitele z config. yaml, proto aby se názvy shodovaly a ověření certifikátu TLS bylo úspěšné, měli byste zadat parametr GatewayHostName v malých případech.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Příklad IoT Edge hierarchie certifikátů

K ilustraci příkladu této cesty k certifikátu je následující snímek obrazovky ze fungujícího IoT Edge zařízení nastaveného jako transparentní brána. OpenSSL se používá pro připojení k centru IoT Edge, ověřování a vystavování certifikátů.

![Snímek obrazovky s hierarchií certifikátů na každé úrovni](./media/iot-edge-certs/iotedge-cert-chain.png)

Můžete zobrazit hierarchii hloubky certifikátu reprezentované na snímku obrazovky:

| Certifikát kořenové certifikační autority         | Pouze test certifikátu certifikační autority Azure IoT Hub                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Certifikát zprostředkující certifikační autority | Pouze test pro zprostředkující certifikát Azure IoT Hub                                                                 |
| Certifikát certifikační autority zařízení       | iotgateway.ca ("iotgateway" byl předán jako název hostitele brány < > do snadno ovladatelného skriptu)   |
| Certifikát CA pro úlohy     | CA pro úlohy iotedge                                                                                       |
| Certifikát serveru IoT Edge hub | iotedgegw. Local (odpovídá názvu "hostname" z config. yaml)                                            |

## <a name="next-steps"></a>Další kroky

[Vysvětlení modulů Azure IoT Edge](iot-edge-modules.md)

[Konfigurace zařízení IoT Edge tak, aby fungovalo jako transparentní brána](how-to-create-transparent-gateway.md)
