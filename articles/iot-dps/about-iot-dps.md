---
title: Přehled služby Azure IoT Hub Device Provisioning Service | Microsoft Docs
description: Popisuje zřizování zařízení v Azure pomocí služby Device Provisioning (DPS) a IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: overview
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: e58028495da409df00db8e25adf68945a008bc9e
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228295"
---
# <a name="what-is-azure-iot-hub-device-provisioning-service"></a>Co je služba Azure IoT Hub Device Provisioning?
Microsoft Azure poskytuje bohatou sadu integrovaných veřejných cloudových služeb pro všechny potřeby vašeho řešení IoT. IoT Hub Device Provisioning Service (DPS) je pomocná služba pro IoT Hub, která umožňuje nulovému zřizování za běhu do správného služby IoT Hub bez nutnosti zásahu člověka. DPS umožňuje bezpečným a škálovatelným způsobem zřizovat miliony zařízení.

## <a name="when-to-use-device-provisioning-service"></a>Kdy použít službu Device Provisioning
Existuje mnoho scénářů zřizování, ve kterých je DPS vhodná volba pro získání zařízení připojených a nakonfigurovaných na IoT Hub, například:

* Plně automatizované zřizování do jednoho řešení IoT bez nutnosti pevně kódovat informace o připojení ke službě IoT Hub v továrně (počáteční nastavení)
* Zařízení pro vyrovnávání zatížení napříč několika rozbočovači
* Připojení zařízení k řešení IoT svého vlastníka na základě dat transakcí prodeje (víceklientská)
* Připojování zařízení ke konkrétnímu řešení IoT v závislosti na případu použití (izolace řešení)
* Připojování zařízení k centru IoT s nejnižší latencí (geografické horizontální dělení)
* Opětovné zřizování na základě změny v zařízení
* Obměna klíčů, které zařízení používá k připojení ke službě IoT Hub (pokud k připojení nepoužívá certifikáty X.509)

## <a name="behind-the-scenes"></a>Informace pro pokročilé uživatele
Všechny scénáře uvedené v předchozí části se dají udělat pomocí DPS pro bezkontaktní zřizování se stejným tokem. Mnohé z ručních kroků tradičně zapojených do zřizování jsou automatizované s DPS, aby se zkrátila doba nasazení zařízení IoT a snížila riziko ruční chyby. Následující část popisuje, co se při zřizování zařízení děje na pozadí. První krok se provádí ručně, ale všechny následující kroky jsou automatizované.

![Základní postup zřizování](./media/about-iot-dps/dps-provisioning-flow.png)

1. Výrobce zařízení přidá informace o registraci zařízení do seznamu registrací na webu Azure Portal.
2. Zařízení kontaktuje koncový bod DPS nastavený ve výrobě. Zařízení předá své identifikaci své identitě v DPS.
3. DPS ověří identitu zařízení tím, že ověří ID registrace a klíč proti položce seznamu registrací s použitím výzvy nonce ([Trusted Platform Module](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) nebo standardního ověřování x. 509 (x. 509).
4. DPS zaregistruje zařízení ve službě IoT Hub a naplní požadovaný doplněný [stav](../iot-hub/iot-hub-devguide-device-twins.md)zařízení.
5. IoT Hub vrátí informace o ID zařízení do DPS.
6. DPS vrátí informace o připojení ke službě IoT Hub k zařízení. Zařízení teď může začít odesílat data přímo do centra IoT.
7. Zařízení se připojí k centru IoT.
8. Zařízení získá požadovaný stav ze svého dvojčete zařízení v centru IoT.

## <a name="provisioning-process"></a>Proces zřizování
V procesu nasazování zařízení, ve kterém se používá DPS, je třeba provést dva samostatné kroky, které se dají udělat nezávisle:

* **Krok výroby**, při kterém se zařízení vytvoří a připraví v továrně.
* **Krok nastavení cloudu**, při kterém se ve službě Device Provisioning nakonfiguruje automatizované zřizování.

Oba tyto kroky se bezproblémově integrují do stávajících výrobních procesů a procesů nasazení. DPS dokonce zjednodušuje některé procesy nasazení, které zahrnují ruční práci k získání informací o připojení do zařízení.

### <a name="manufacturing-step"></a>Krok výroby
Tento krok zahrnuje vše, co se děje na výrobní lince. Mezi role zapojené do tohoto kroku patří návrhář čipu, výrobce čipu, integrátor nebo koncový výrobce zařízení. Tento krok se týká samotného vytvoření hardwaru.

DPS nezavádí do výrobního procesu nový krok; místo toho se přidělí ke stávajícímu kroku, který nainstaluje počáteční software a (v ideálním případě) modul HSM na zařízení. Místo vytváření ID zařízení se v tomto kroku v zařízení naprogramují informace o službě zřizování, což zařízení umožní po zapnutí zavolat službu zřizování a získat informace o připojení nebo přiřazení řešení IoT.

V tomto kroku také výrobce zařízení poskytne nasazovači nebo operátorovi zařízení klíčové identifikující informace. Poskytnutí těchto informací může být jednoduché a spočívat pouze v potvrzení, že všechna zařízení mají certifikát X.509 vygenerovaný z podpisového certifikátu od nasazovače nebo operátora zařízení. Nebo může být složité a spočívat v extrahování veřejné části ověřovacího klíče TPM ze všech zařízení TPM. Tyto služby v současné době poskytuje celá řada výrobců čipů.

### <a name="cloud-setup-step"></a>Krok nastavení cloudu
Tento krok se týká konfigurace cloudu pro správné automatické zřizování. Do kroku nastavení cloudu se obvykle zapojují dva typy uživatelů: někdo, kdo ví, jaké má být počáteční nastavení zařízení (operátor zařízení), a někdo jiný, kdo ví, jak se mají zařízení rozdělit mezi centra IoT (operátor řešení).

Zřizování se musí zpočátku jednorázově nastavit, což obvykle provádí operátor řešení. Po nakonfigurování služby zřizování není potřeba ji upravovat, dokud se nezmění případ použití.

Po nakonfigurování služby pro automatické zřizování se služba musí připravit na registrování zařízení. Tento krok provádí operátor zařízení, který zná požadovanou konfiguraci zařízení a má na starosti zajištění, aby služba zřizování mohla při hledání odpovídajícího centra IoT správně ověřit identitu zařízení. Operátor zařízení vezme klíčové identifikující informace od výrobce a přidá je do seznamu registrací. Seznam registrací se následně může aktualizovat s ohledem na přidávání nových položek nebo aktualizaci nejnovějších informací o zařízeních ve stávajících položkách.

## <a name="registration-and-provisioning"></a>Registrace a zřizování
Termín *zřizování* znamená různé věci v závislosti na odvětví, ve kterém se použije. V kontextu zřizování zařízení IoT v cloudovém řešení je zřizování proces skládající se ze dvou částí:

1. První část představuje navázání počátečního připojení mezi zařízením a řešením IoT prostřednictvím registrace zařízení.
2. Druhá část představuje použití správné konfigurace pro zařízení na základě konkrétních požadavků řešení, do kterého se zaregistrovalo.

Po dokončení obou těchto kroků můžeme říct, že je zařízení plně zřízené. Některé cloudové služby zajišťují pouze první krok procesu zřizování, tedy registraci zařízení do koncového bodu řešení IoT, ale už ne počáteční konfiguraci. DPS automatizuje oba kroky k zajištění bezproblémového zřizování zařízení.

## <a name="features-of-the-device-provisioning-service"></a>Funkce služby Device Provisioning
DPS má mnoho funkcí, což je ideální pro zřizování zařízení.

* Podpora **zabezpečeného osvědčení** pro identity založené na X.509 i TPM.
* **Seznam registrací** obsahující úplné záznamy o zařízeních a skupinách zařízení, která se někdy můžou registrovat. Jakmile se zařízení zaregistruje, bude seznam registrací obsahovat informace o požadované konfiguraci zařízení a může se kdykoli aktualizovat.
* **Vícenásobné zásady přidělování** , kterými se řídí, jak DPS přiřadí zařízení do centra IoT v podpoře vašich scénářů: nejnižší latence, rovnoměrně vážená distribuce (výchozí) a statická konfigurace prostřednictvím seznamu registrací. Latence je určena pomocí stejné metody jako [Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md#performance).
* **Monitorování a protokolování diagnostiky** pro zajištění, že vše funguje správně.
* **Podpora pro více rozbočovačů** umožňuje DPS přiřazovat zařízení do více než jednoho centra IoT. DPS může komunikovat s centry napříč několika předplatnými Azure.
* **Podpora pro různé oblasti** umožňuje DPS přiřazovat zařízení do centra IoT v jiných oblastech.
* **Šifrování dat v klidovém** stavu umožňuje, aby se data v DPS zašifroval a dešifroval transparentně pomocí 256 šifrování AES, což je jedno z nejúčinnějších šifrovacích šifr, která jsou kompatibilní se standardem FIPS 140-2.


Další informace o konceptech a funkcích, které jsou součástí zřizování zařízení, najdete v tématu věnovaném [terminologii DPS](concepts-service.md) spolu s dalšími koncepčními tématy ve stejné části.

## <a name="cross-platform-support"></a>Podpora různých platforem
Podobně jako všechny služby Azure IoT funguje DPS pro různé platformy s různými operačními systémy. Azure nabízí open source sady SDK v různých [jazycích](https://github.com/Azure/azure-iot-sdks), které usnadňují připojení zařízení a správu služby. DPS podporuje následující protokoly pro připojení zařízení:

* HTTPS
* AMQP
* AMQP přes webové sokety
* MQTT
* MQTT přes webové sokety

DPS podporuje jenom připojení HTTPS pro operace služby.

## <a name="regions"></a>Oblasti
DPS je k dispozici v mnoha oblastech. Aktualizovaný seznam stávajících a nově ohlášených oblastí pro všechny služby najdete na stránce [Oblasti Azure](https://azure.microsoft.com/regions/). Dostupnost služby Device Provisioning můžete zkontrolovat na stránce [Stav Azure](https://azure.microsoft.com/status/).

> [!NOTE]
> DPS je globální a není vázána na umístění. Je však nutné zadat oblast, ve které budou uložena Metadata přidružená k profilu DPS.

## <a name="availability"></a>Dostupnost
K dispozici je 99,9% smlouva SLA pro DPS a můžete [si přečíst smlouvu SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/). Úplná smlouva [Azure SLA](https://azure.microsoft.com/support/legal/sla/) vysvětluje garantovanou dostupnost Azure jako celku.

## <a name="quotas"></a>Kvóty
Pro každé předplatné Azure platí výchozí omezení kvót, která můžou ovlivnit dosah vašeho řešení IoT. Aktuální omezení je 10 služeb Device Provisioning pro každé předplatné.

[!INCLUDE [azure-iotdps-limits](../../includes/iot-dps-limits.md)]

Další podrobnosti o limitech kvót:
* [Omezení služeb v předplatném Azure](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>Související komponenty Azure
DPS automatizuje zřizování zařízení pomocí Azure IoT Hub. Přečtěte si další informace o [IoT Hub](../iot-hub/index.yml).

## <a name="next-steps"></a>Další kroky
Teď máte přehled o zřizování zařízení IoT v Azure. Dalším krokem je vyzkoušet si kompletní scénář IoT.

[Nastavení IoT Hub Device Provisioning Service s Azure Portal](quick-setup-auto-provision.md)

[Vytvoření a zřízení simulovaného zařízení](quick-create-simulated-device.md)

[Nastavení zařízení pro zřizování](tutorial-set-up-device.md)