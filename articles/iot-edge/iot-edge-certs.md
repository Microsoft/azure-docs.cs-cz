---
title: Certifikáty pro zabezpečení zařízení – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Azure IoT Edge používá certifikát k ověření zařízení, modulů a listových zařízení a navázání zabezpečených připojení mezi nimi.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 58294c7afdf31ddd29611351d6442db1c4966157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269034"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Zjistěte, jak Azure IoT Edge používá certifikáty

Certifikáty IoT Edge používají moduly a následná zařízení IoT k ověření identity a legitimity modulu runtime [centra IoT Edge.](iot-edge-runtime.md#iot-edge-hub) Tato ověření umožňují zabezpečené připojení TLS (zabezpečení transportní vrstvy) mezi modulem runtime, moduly a zařízeními IoT. Stejně jako i samotné centrum IoT, ioT Edge vyžaduje zabezpečené a šifrované připojení z ioT po datovaných (nebo listových) zařízení a modulů IoT Edge. Chcete-li vytvořit zabezpečené připojení TLS, modul centra IoT Edge představuje řetěz certifikátů serveru pro připojení klientů, aby mohli ověřit svou identitu.

Tento článek vysvětluje, jak certifikáty IoT Edge mohou fungovat ve scénářích výroby, vývoje a testování. Zatímco skripty jsou různé (Powershell vs bash), pojmy jsou stejné mezi Linuxem a Windows.

## <a name="iot-edge-certificates"></a>Certifikáty IoT Edge

Výrobci obvykle nejsou koncovými uživateli zařízení IoT Edge. Někdy jediný vztah mezi těmito dvěma je, když koncový uživatel nebo operátor, nákupy obecné zařízení provedené výrobcem. Jindy, výrobce pracuje na základě smlouvy vybudovat vlastní zařízení pro provozovatele. Návrh certifikátu IoT Edge se pokusí vzít v úvahu oba scénáře.

Následující obrázek znázorňuje ioT Edge použití certifikátů. Mezi kořenovým certifikátem certifikační autority a certifikátem certifikační autority zařízení může být nula, jeden nebo více zprostředkujících podpisových certifikátů v závislosti na počtu zúčastněných entit. Zde ukážeme jeden případ.

![Diagram typických vztahů certifikátů](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Certifikační autorita

Certifikační autorita, zkráceně Certifikační autorita, je entita, která vydává digitální certifikáty. Certifikační autorita funguje jako důvěryhodná třetí strana mezi vlastníkem a příjemcem certifikátu. Digitální certifikát osvědčuje vlastnictví veřejného klíče příjemcem certifikátu. Řetěz důvěryhodných certifikátů funguje tak, že zpočátku vydává kořenový certifikát, který je základem pro důvěryhodnost ve všech certifikátech vydaných úřadem. Poté může vlastník použít kořenový certifikát k vydání dalších zprostředkujících certifikátů ("leaf" certifikáty).

### <a name="root-ca-certificate"></a>Kořenový certifikát certifikační autority

Kořenový certifikát certifikační autority je kořenovým adresářem důvěryhodnosti celého procesu. V produkčních scénářích je tento certifikát certifikační autority obvykle zakoupen od důvěryhodné ho komerčního certifikačního úřadu, jako je Baltimore, Verisign nebo DigiCert. Pokud máte úplnou kontrolu nad zařízeními, která se připojují k zařízením IoT Edge, je možné použít certifikační autoritu na podnikové úrovni. V obou těchto případě celý řetěz certifikátů z centra IoT Edge nahoru se shrnuje k němu, takže leaf IoT zařízení musí důvěřovat kořenový certifikát. Kořenový certifikát certifikační autority můžete uložit buď do úložiště důvěryhodných kořenových certifikačních autorit, nebo zadat podrobnosti o certifikátu v kódu aplikace.

### <a name="intermediate-certificates"></a>Zprostředkující certifikáty

V typickém výrobním procesu pro vytváření zabezpečených zařízení se certifikáty kořenové certifikační autority používají jen zřídka přímo, především kvůli riziku úniku nebo expozice. Kořenový certifikát certifikační autority vytvoří a digitálně podepíše jeden nebo více zprostředkujících certifikátů certifikační autority. Může existovat pouze jeden nebo může existovat řetězec těchto zprostředkujících certifikátů. Scénáře, které by vyžadovaly řetězec zprostředkujících certifikátů, zahrnují:

* Hierarchie oddělení v rámci výrobce.

* Více společností, které se sériově podílely na výrobě zařízení.

* Zákazník, který si koupí kořenovou certifikační autoritu a odvodí podpisový certifikát pro výrobce, aby podepsal zařízení, která vyrábí jménem tohoto zákazníka.

V každém případě výrobce použije zprostředkující certifikát certifikační autority na konci tohoto řetězce k podepsání certifikátu certifikační autority zařízení umístěného na koncovém zařízení. Obecně platí, že tyto mezilehlé certifikáty jsou pečlivě střeženy ve výrobním závodě. Pro své použití procházejí přísnými procesy, fyzickými i elektronickými.

### <a name="device-ca-certificate"></a>Certifikát certifikační autority zařízení

Certifikát certifikační autority zařízení je generován a podepsán konečným zprostředkujícím certifikátem certifikační autority v procesu. Tento certifikát je nainstalován na samotném zařízení IoT Edge, nejlépe v zabezpečeném úložišti, jako je například modul hardwarového zabezpečení (HSM). Certifikát certifikační autority zařízení navíc jednoznačně identifikuje zařízení IoT Edge. Certifikát certifikační autority zařízení může podepsat další certifikáty.

### <a name="iot-edge-workload-ca"></a>Certifikační autorita úlohy IoT Edge

[Správce zabezpečení IoT Edge](iot-edge-security-manager.md) generuje certifikát certifikační autority pracovního vytížení, první na straně "operátora" procesu při prvním spuštění IoT Edge. Tento certifikát je generován a podepsán "certifikátem certifikační autority zařízení". Tento certifikát, který je pouze dalším zprostředkujícím podpisovým certifikátem, se používá ke generování a podepisování dalších certifikátů používaných runtimem IoT Edge. Dnes je to především certifikát serveru centra IoT Edge popisovaný v následující části, ale v budoucnu může zahrnovat další certifikáty pro ověřování součástí IoT Edge.

### <a name="iot-edge-hub-server-certificate"></a>Certifikát centrálního serveru IoT Edge

Certifikát serveru centra IoT Edge je skutečný certifikát prezentovaný zařízením a modulům listu pro ověření identity během vytvoření připojení TLS vyžadovaného technologií IoT Edge. Tento certifikát představuje celý řetězec podpisových certifikátů, které se používají ke generování až do kořenového certifikátu certifikační autority, kterému musí zařízení IoT listu důvěřovat. Při generování Správce zabezpečení IoT Edge, běžný název (CN), tohoto certifikátu centra IoT Edge je nastavena na vlastnost 'hostname' v config.yaml souboru po převodu na malá písmena. Tato konfigurace je běžným zdrojem záměny s IoT Edge.

## <a name="production-implications"></a>Důsledky výroby

Rozumnou otázkou může být "proč IoT Edge potřebuje certifikát navíc k certifikační autoritě zatížení?? Nemohl použít certifikát certifikační autority zařízení k přímému generování certifikátu serveru centra IoT Edge?". Technicky by mohlo. Účelem tohoto zprostředkujícího certifikátu "pracovní zátěže" je však oddělit obavy mezi výrobcem zařízení a provozovatelem zařízení. Představte si scénář, kdy se zařízení IoT Edge prodává nebo převádí od jednoho zákazníka k druhému. Pravděpodobně byste chtěli, aby certifikát certifikační autority zařízení dodaný výrobcem byl neměnný. Certifikáty "zatížení" specifické pro provoz zařízení by však měly být vymazány a znovu vytvořeny pro nové nasazení.

Vzhledem k tomu, že výrobní a provozní procesy jsou odděleny, zvažte při přípravě výrobních zařízení následující důsledky:

* Při jakémkoli procesu založeném na certifikátu by měl být kořenový certifikát certifikační autority a všechny zprostředkující certifikáty certifikační autority zabezpečeny a monitorovány během celého procesu zavádění zařízení IoT Edge. Výrobce zařízení IoT Edge by měl mít silné procesy pro správné ukládání a používání jejich zprostředkujících certifikátů. Kromě toho by měl být certifikát certifikační autority zařízení uložen v co nejbezpečnějším úložišti na samotném zařízení, nejlépe v modulu hardwarového zabezpečení.

* Certifikát serveru centra IoT Edge je prezentován centrem IoT Edge pro připojení klientských zařízení a modulů. Běžný název (CN) certifikátu certifikační autority zařízení **se nesmí shodovat** s názvem "hostname", který bude použit v souboru config.yaml na zařízení IoT Edge. Název používaný klienty pro připojení k IoT Edge (například prostřednictvím parametru GatewayHostName připojovacího řetězce nebo příkazu CONNECT v MQTT) **nemůže být** stejný jako běžný název používaný v certifikátu certifikační autority zařízení. Toto omezení je, protože centrum IoT Edge představuje celý svůj řetězec certifikátů pro ověření klienty. Pokud mají certifikát serveru ioT Edge a certifikát certifikační autority zařízení stejnou cn, získáte ověřovací smyčku a certifikát zruší platnost.

* Vzhledem k tomu, že certifikát certifikační autority zařízení používá dém zabezpečení IoT Edge ke generování konečných certifikátů IoT Edge, musí být sám podpisovým certifikátem, což znamená, že má možnosti podepisování certifikátů. Použití certifikátu certifikační autority V3 Basic pro omezení ca:True automaticky nastaví požadované vlastnosti použití klíče.

>[!Tip]
> Pokud jste již prošli nastavení IoT Edge jako transparentní brána ve scénáři pro vývoj a testování pomocí našich "skriptů pro pohodlí" (viz další část) a použili stejný název hostitele při vytváření certifikátu certifikační autority zařízení jako u názvu hostitele v config.yaml, možná se divíte, proč to fungovalo. Ve snaze zjednodušit prostředí pro vývojáře, pohodlí skripty připojí ".ca" na konci názvu, který předáte do skriptu. Takže například pokud jste použili "mygateway" pro název zařízení ve skriptech a název hostitele v config.yaml, první bude převedena na mygateway.ca před použitím jako cn pro certifikát certifikační autority zařízení.

## <a name="devtest-implications"></a>Důsledky pro vývoj a testování

Pro usnadnění vývoje a testování scénářů, Microsoft poskytuje sadu [skriptů pohodlí](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) pro generování neprodukční certifikáty vhodné pro IoT Edge ve scénáři transparentní brány. Příklady fungování skriptů najdete v [tématu Vytvoření ukázkových certifikátů pro testování funkcí zařízení IoT Edge](how-to-create-test-certificates.md).

>[!Tip]
> Chcete-li připojit zařízení IoT "list" zařízení a aplikace, které používají naše zařízení IoT SDK přes IoT Edge, musíte přidat volitelný GatewayHostName parametr na konec připojovacího řetězce zařízení. Při generování certifikátu serveru Edge Hub je založen na malé písmena verze název hostitele z config.yaml, proto pro názvy, které mají odpovídat a ověření certifikátu TLS úspěšné, měli byste zadat GatewayHostName parametr v malých písmen.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Příklad hierarchie certifikátů IoT Edge

Pro ilustraci příkladu této cesty k certifikátu je následující snímek obrazovky z funkčního zařízení IoT Edge nastaveného jako transparentní brána. OpenSSL se používá k připojení k centru IoT Edge, ověření a vypáčení certifikátů.

![Snímek obrazovky s hierarchií certifikátů na každé úrovni](./media/iot-edge-certs/iotedge-cert-chain.png)

Na snímku obrazovky můžete zobrazit hierarchii hloubky certifikátu:

| Kořenový certifikát certifikační autority         | Jenom test certifikátu certifikátu certifikační autority služby Azure IoT Hub                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Zprostředkující certifikát certifikační autority | Pouze test průběžného certifikátu centra Azure IoT Hub                                                                 |
| Certifikát certifikační autority zařízení       | iotgateway.ca ("iotgateway" byla předána jako název hostitele brány < > do skriptů pohodlí)   |
| Certifikát certifikační autority pracovního vytížení     | iotedge pracovní zátěž ca                                                                                       |
| Certifikát serveru Centra IoT Edge Hub | iotedgegw.local (odpovídá 'hostname' z config.yaml)                                            |

## <a name="next-steps"></a>Další kroky

[Vysvětlení modulů Azure IoT Edge](iot-edge-modules.md)

[Konfigurace zařízení IoT Edge tak, aby fungovalo jako transparentní brána](how-to-create-transparent-gateway.md)
