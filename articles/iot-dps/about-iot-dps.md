---
title: Přehled Azure IoT Hub zařízení zřizování služby | Microsoft Docs
description: Popisuje zřizování zařízení v Azure pomocí služby zřizování zařízení a služby IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 12/05/2017
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 45f47a553f94da2759c4db2b79c8ef5a1b42b8e8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630236"
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service"></a>Zřizování zařízení službou Azure IoT Hub zařízení zřizování
Microsoft Azure poskytuje bohatou sadu integrovaných veřejné cloudové služby pro všechny vaše potřeby řešení IoT. Zřizování služby zařízení IoT Hub je služba Pomocník pro IoT Hub, která umožňuje nula touch, za běhu zřizování do správné služby IoT hub bez nutnosti lidského zásahu povolení zákazníkům zřídit miliony zařízení zabezpečený a škálovatelné způsobem.

## <a name="when-to-use-device-provisioning-service"></a>Kdy použít službu zřizování zařízení
Existuje mnoho zřizování scénářů, ve kterých služba zřizování zařízení je ideální volbou pro získávání zařízení připojené a nakonfigurovat tak, aby Centrum IoT, jako například:

* Nula touch zřizování na jediném řešení IoT bez hardcoding informace o připojení služby IoT Hub v objektu pro vytváření (počáteční instalace)
* Vyrovnávání zatížení zařízení napříč více rozbočovače
* Připojení zařízení k jejich vlastníkovi řešení IoT na základě dat prodejní transakce (víceklientská)
* Připojení zařízení k konkrétní řešení IoT v závislosti na případ použití (řešení izolace)
* Připojení zařízení do služby IoT hub s nejnižší latencí (geograficky horizontálního dělení)
* Znovu zřizování podle změn v zařízení
* Vrácení klíčů používaných zařízení pro připojení ke službě IoT Hub (Pokud se nepřipojujete pomocí certifikátů X.509)

## <a name="behind-the-scenes"></a>Informace pro pokročilé uživatele
Všechny scénáře uvedené v předchozí části lze provést pomocí zřizování služby pro zřizování nula touch s stejného toku. Řadu Ruční kroky tradičně zřizování jsou automatizované službou zřizování zařízení zkrátit čas nasazení zařízení IoT a snížení rizika ruční chyby. Následuje popis co se děje na pozadí zařízení zřízený. Prvním krokem je ruční, se automatizují všechny následující kroky.

![Základní postup zřizování](./media/about-iot-dps/dps-provisioning-flow.png)

1. Výrobce zařízení přidá informace o registraci zařízení do seznamu registrace na portálu Azure.
2. Zařízení kontaktuje zřizování koncovému bodu služby nastavena v objektu pro vytváření. Zařízení předá zřizování služby jeho identifikační informace k prokázání své identity.
3. Zřizování služby ověří identitu zařízení s těmito registrační ID a klíč pro položku seznamu registrace pomocí nonce výzvy ([Trusted Platform Module](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) nebo standardní ověření (X.509 X.509).
4. Zřizování služby registruje zařízení služby IoT hub a naplní zařízení [požadovaného stavu twin](../iot-hub/iot-hub-devguide-device-twins.md).
5. Služba IoT hub vrací informace o ID zařízení ke službě zřizování.
6. Zřizování služby vrátí informace o IoT hub připojení do zařízení. Zařízení nyní můžete spustit odesílání dat přímo ke službě IoT hub.
7. Připojení zařízení do služby IoT hub.
8. Zařízení získá požadovaný stav z jeho dvojče zařízení IoT hub.

## <a name="provisioning-process"></a>Zřizování
V procesu nasazení zařízení, ve kterém služba zřizování zařízení trvá část, kterou lze provést nezávisle jsou odlišné dva kroky:

* **Výrobní krok** kterými zařízení vytvořit a připravené na objekt factory, a
* **Cloudu kroku** v službu zřizování zařízení je nakonfigurován pro automatizované zajišťování.

Oba tyto kroky nevešla bezproblémově s stávající procesy výrobní a nasazení. Službu zřizování zařízení zjednodušuje i některé procesy nasazení, které zahrnují velké množství ručního nastavení, chcete-li získat informace o připojení do zařízení.

### <a name="manufacturing-step"></a>Výrobní krok
Tento krok je o tom, co se stane na výrobní řádku. Role zahrnutých v tomto kroku zahrnují silicon designer, silicon výrobce, Integrátor nebo end výrobce zařízení. Tento krok se týká vytvoření samotného hardwaru.

Službu zřizování zařízení nezavádí nové krokem v procesu výrobní; Místo toho sváže do existující krok, který nainstaluje software, počáteční a (v ideálním případě) modul hardwarového zabezpečení na zařízení. Místo vytváření ID zařízení v tomto kroku, je zařízení jednoduše naprogramovat se informace o zřizování služby, zavolá službu zřizování získat jeho přiřazení připojení informace nebo IoT řešení, pokud je zapnutá.

V tomto kroku také výrobce poskytuje nástroje pro nasazení nebo operátor zařízení identifikovat informace o klíči. To může být stejně jednoduché jako potvrzení, že všechna zařízení, mají certifikát X.509 vygenerovat z podpisový certifikát poskytuje nástroje pro nasazení nebo operátor zařízení, k extrahování veřejnou část ověřovacího klíče TPM z každé zařízení TPM. Tyto služby jsou nabízeny spousta výrobců silicon ještě dnes.

### <a name="cloud-setup-step"></a>Krok nastavení cloudu
Tento krok je o konfiguruje se cloud pro správné automatické zřizování. Obecně existují dva typy uživatelů zahrnutých v kroku instalace cloudu: někoho, kdo vědět, jak zařízení musí být úvodního nastavení (operátor zařízení) a někdo jiný, který zná, jak jsou zařízení, aby se daly rozdělit mezi centra IoT (operátor řešení).

Je jednorázové počáteční nastavení zřizování, který se musí vyskytovat a tato úloha je obecně provádí operátor řešení. Po nakonfigurování zřizování služby nemá upravit, pokud případ použití změny.

Služba byla nakonfigurována pro automatické zřizování, musí být připraveno k registraci zařízení. Tento krok je potřeba zařízení operátor, který zná požadovanou konfiguraci zařízení a má na starosti tak, že zřizování služby můžete správně ověřit identitu pro identity zařízení při přechodu do hledá své služby IoT hub. Operátor zařízení trvá identifikační informace o klíči od výrobce a přidá do seznamu registrace. Může být následné aktualizace seznamu registrace se přidají nové položky nebo existující položky jsou aktualizované o nejnovější informace o zařízeních.

## <a name="registration-and-provisioning"></a>Registrace a zřizování
*Zřizování* znamená různých věcí, podle toho, ve kterém se používá termín odvětví. V rámci zřizování zařízení IoT a jejich cloudové řešení zřizování je proces dvě části:

1. První část je vytvoření počátečního připojení mezi zařízením a řešení IoT tak, že zaregistrujete zařízení.
2. Druhá část je zařízení v závislosti na požadavcích konkrétní řešení, který byl zaregistrován pro použití správnou konfiguraci.

Pouze, když byly dokončeny tyto dva kroky můžete říkáme, že má zařízení plně zřízený. Některé cloudové služby pouze poskytují prvním krokem procesu zřizování, registraci zařízení ke koncovému bodu řešení IoT, ale neposkytuje počáteční konfigurace. Službu zřizování zařízení automatizuje oba kroky a poskytuje bezproblémové prostředí zřizování pro zařízení.

## <a name="features-of-the-device-provisioning-service"></a>Funkce zřizování služby zařízení
Službu zřizování zařízení obsahuje mnoho funkcí, které ideální pro zřizování zařízení.

* **Zabezpečení ověření** podpora X.509 a identity založené na čipu TPM.
* **Registrace seznamu** obsahující úplný záznam zařízení nebo skupiny zařízení, která může na některých bodu registrace. Registrace seznam obsahuje informace o požadovanou konfiguraci zařízení po registraci, a můžete kdykoli aktualizovat.
* **Více zásady přidělování** řídit, jak službu zřizování zařízení přiřadí zařízení do služby IoT hubs podporu vaše scénáře.
* **Monitorovací a diagnostické protokoly** a ujistěte se, všechno funguje správně.
* **Podpora více rozbočovače** který umožňuje služby zřizování zařízení přiřadit zařízení k více než jeden IoT hub. Službu zřizování zařízení může komunikovat centra napříč několika předplatných Azure.
* **Podpora mezi oblastmi** který umožňuje služby zřizování zařízení přiřadit zařízení k centra IoT v jiných oblastech.

Další informace o konceptech a funkce, které se účastní zřizování zařízení v [zařízení koncepty](concepts-device.md), [služby koncepty](concepts-service.md), a [koncepty zabezpečení](concepts-security.md).

## <a name="cross-platform-support"></a>Podpora více platforem
Zřizování službu zařízení, jako jsou všechny služby Azure IoT, funguje napříč platformami s různými operačními systémy. Azure nabízí otevřete zdroje sady SDK v různých [jazyky](https://github.com/Azure/azure-iot-sdks) k usnadnění připojení zařízení a Správa služby. Službu zřizování zařízení podporuje následující protokoly pro připojení zařízení:

* HTTPS
* AMQP
* AMQP přes objekty websockets
* MQTT
* MQTT přes objekty websockets

Službu zřizování zařízení podporuje pouze připojení prostřednictvím protokolu HTTPS pro operace služby.

## <a name="regions"></a>Oblasti
Službu zřizování zařízení, je k dispozici v mnoha oblastech. Uchováváme aktualizovaný seznam stávajících a nově oznámeno oblasti pro všechny služby v [oblasti Azure](https://azure.microsoft.com/regions/). Můžete zjistit, kde je k dispozici na službu zřizování zařízení [stavu Azure](https://azure.microsoft.com/status/) stránky.

> [!NOTE]
> Službu zřizování zařízení je globální a není vázaná na umístění. Však musíte zadat oblast, ve kterém se bude nacházet budou metadata spojená s profilem služby zřizování zařízení.

## <a name="availability"></a>Dostupnost
Uchováváme 99,9 % Smlouvy o úrovni služeb pro službu zřizování zařízení a můžete [číst smlouvě SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/). Úplná smlouva [Azure SLA](https://azure.microsoft.com/support/legal/sla/) vysvětluje garantovanou dostupnost Azure jako celku.

## <a name="quotas"></a>Kvóty
Každé předplatné Azure má výchozí kvótami v místě, ke kterému by mohlo mít vliv oboru řešení IoT. Aktuální limit na základě za předplatné je 10 zařízení zřizování služeb podle předplatného.

Další podrobnosti o limitech kvót:

* [Omezení služeb v předplatném Azure](../azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>Související komponenty Azure
Službu zřizování zařízení automatizuje zřizování zařízení s Azure IoT Hub. Další informace o [IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

## <a name="next-steps"></a>Další postup
Teď máte přehled o zřizování zařízení IoT v Azure. Dalším krokem je můžete vyzkoušet na scénář IoT od začátku do konce.
> [!div class="nextstepaction"]
> [Nastavení služby zřizování zařízení IoT Hub pomocí portálu Azure](quick-setup-auto-provision.md)
> [vytvořit a zřídit simulované zařízení](quick-create-simulated-device.md)
> [nastavit zařízení pro zřizování](tutorial-set-up-device.md)
