---
title: zahrnout soubor
description: zahrnout soubor
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 08cca67455df4b2d28bba0a7410fccc11446fcdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010690"
---
Tento článek poskytuje další úroveň podrobností pro zabezpečení infrastruktury Internet věcí založené na službě Azure IoT (IoT). Odkazuje na podrobnosti úrovně implementace pro konfiguraci a nasazení jednotlivých komponent. Nabízí také porovnání a volby mezi různými konkurenčními metodami.

Zabezpečení nasazení Azure IoT se dá rozdělit do těchto tří oblastí zabezpečení:

* **Zabezpečení zařízení**: zabezpečení zařízení IoT při jeho nasazení v nevolném režimu.

* **Zabezpečení připojení**: Zajistěte, aby byla veškerá data přenášená mezi zařízením IoT a IoT Hub důvěrná a oprávněná kontrola.

* **Zabezpečení cloudu**: poskytuje prostředky k zabezpečení dat při jejich přesouvání a ukládá se do cloudu.

![Tři oblasti zabezpečení](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Zabezpečené zřizování a ověřování zařízení

Akcelerátory řešení IoT zabezpečují zařízení IoT pomocí následujících dvou metod:

* Poskytnutím jedinečného klíče identity (tokeny zabezpečení) pro každé zařízení, které může zařízení používat ke komunikaci s IoT Hub.

* Pomocí [certifikátu X. 509](https://www.itu.int/rec/T-REC-X.509-201210-S) na zařízení a privátního klíče jako prostředku k ověření zařízení pro IoT Hub. Tato metoda ověřování zajišťuje, že privátní klíč v zařízení není kdykoli v zařízení znám, což zajišťuje vyšší úroveň zabezpečení.

Metoda tokenu zabezpečení poskytuje ověřování pro každé volání, které zařízení provedlo, k IoT Hub přiřazením symetrického klíče ke každému volání. Ověřování pomocí X. 509 umožňuje ověřování zařízení IoT ve fyzické vrstvě v rámci zřízení připojení TLS. Metoda založená na tokenech zabezpečení se dá použít bez ověřování X. 509, což je méně zabezpečený vzor. Volba mezi těmito dvěma metodami je primárně vyřízena způsobem, jakým je nutné zabezpečit ověřování zařízení, a dostupnost zabezpečeného úložiště na zařízení (bezpečné uložení privátního klíče).

## <a name="iot-hub-security-tokens"></a>IoT Hub tokeny zabezpečení

IoT Hub používá k ověřování zařízení a služeb tokeny zabezpečení, aby nedocházelo k posílání klíčů v síti. Tokeny zabezpečení jsou navíc omezené v době platnosti a rozsahu. Sady SDK Azure IoT automaticky generují tokeny bez nutnosti jakékoli speciální konfigurace. Některé scénáře ale vyžadují, aby uživatel vygeneroval a použil tokeny zabezpečení přímo. Mezi tyto scénáře patří přímé použití MQTT, AMQP nebo povrchu HTTP nebo implementace vzoru služby tokenu.

Další podrobnosti o struktuře tokenu zabezpečení a jeho použití najdete v následujících článcích:

* [Struktura tokenu zabezpečení](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [Použití tokenů SAS jako zařízení](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Každý IoT Hub má [registr identit](../articles/iot-hub/iot-hub-devguide-identity-registry.md) , který se dá použít k vytvoření prostředků pro jednotlivé zařízení ve službě, jako je například fronta, která obsahuje zprávy v letadlech a umožňuje přístup k koncovým bodům pro zařízení. IoT Hub registr identit poskytuje zabezpečené úložiště identit zařízení a klíčů zabezpečení pro řešení. Do seznamu povolených nebo blokovaných identit zařízení můžete přidat jednotlivé identity nebo skupiny a povolit tak úplnou kontrolu nad přístupem k zařízení. Následující články poskytují další podrobnosti o struktuře registru identit a podporovaných operacích.

[IoT Hub podporuje protokoly, jako jsou MQTT, AMQP a http](../articles//iot-hub/iot-hub-devguide-security.md). Každý z těchto protokolů používá tokeny zabezpečení ze zařízení IoT pro IoT Hub odlišně:

* AMQP: SASL PLAIN a AMQP zabezpečení založené na deklaracích identity ( `{policyName}@sas.root.{iothubName}` s tokeny na úrovni IoT Hub `{deviceId}` ) s tokeny v oboru zařízení).

* MQTT: pomocí pole pro `{deviceId}` `{ClientId}` `{IoThubhostname}/{deviceId}` **zadání** **uživatelského jména** a tokenu SAS v poli heslo Připojte paket.

* HTTP: platný token je v hlavičce autorizační žádosti.

IoT Hub registru identit lze použít ke konfiguraci pověření zabezpečení jednotlivých zařízení a řízení přístupu. Pokud však řešení IoT již má významnou investici do [vlastního registru identity zařízení nebo schématu ověřování](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication), může být integrováno do existující infrastruktury s IoT Hub vytvořením služby tokenu.

### <a name="x509-certificate-based-device-authentication"></a>Ověřování zařízení založeného na certifikátech X. 509

Použití [certifikátu X. 509 založeného na zařízení](../articles/iot-hub/iot-hub-devguide-security.md) a jeho přidruženého páru privátních a veřejných klíčů umožňuje další ověřování ve fyzické vrstvě. Privátní klíč je bezpečně uložený v zařízení a nemůže být zjistitelný mimo zařízení. Certifikát X. 509 obsahuje informace o zařízení, jako je ID zařízení a další informace o organizaci. Podpis certifikátu se vygeneruje pomocí privátního klíče.

Tok zřizování zařízení vysoké úrovně:

* Přidružte identifikátor k fyzickému zařízení – identitě zařízení nebo k certifikátu X. 509, který je přidružený k zařízení během výroby zařízení nebo provize.

* Vytvoření odpovídající položky identity v IoT Hub – identita zařízení a související informace o zařízení v registru IoT Hub identity.

* Kryptografický otisk certifikátu X. 509 bezpečně ukládejte v IoT Hub registru identit.

### <a name="root-certificate-on-device"></a>Kořenový certifikát na zařízení

Při vytváření zabezpečeného připojení TLS s IoT Hub se zařízení IoT ověřuje IoT Hub pomocí kořenového certifikátu, který je součástí sady SDK pro zařízení. Pro klientskou sadu SDK pro C je certifikát umístěný pod složkou " \\ \\ certifikáty c" v kořenovém adresáři úložiště. I když jsou tyto kořenové certifikáty dlouhodobé, můžou pořád vypršet nebo odvolat. Pokud neexistuje žádný způsob, jak aktualizovat certifikát na zařízení, nemusí být zařízení následně možné připojit se k IoT Hub (nebo jiné cloudové službě). Když je zařízení IoT efektivně nasazené, může se jednat o způsob, jak tento certifikát aktualizovat, protože toto riziko bude zmírnit.

## <a name="securing-the-connection"></a>Zabezpečení připojení

Připojení k internetu mezi zařízením IoT a IoT Hub je zabezpečené pomocí standardu TLS (Transport Layer Security). Azure IoT podporuje [tls 1,2](https://tools.ietf.org/html/rfc5246), TLS 1,1 a TLS 1,0 v tomto pořadí. Podpora TLS 1,0 se poskytuje jenom pro zpětnou kompatibilitu. Podívejte se na [podporu TLS v IoT Hub](../articles/iot-hub/iot-hub-tls-support.md) a zjistěte, jak nakonfigurovat rozbočovač tak, aby používal TLS 1,2, protože poskytuje nejvyšší zabezpečení.

## <a name="securing-the-cloud"></a>Zabezpečení cloudu

Azure IoT Hub umožňuje definici [zásad řízení přístupu](../articles/iot-hub/iot-hub-devguide-security.md) pro každý bezpečnostní klíč. K udělení přístupu ke všem koncovým bodům IoT Hub používá následující sadu oprávnění. Oprávnění omezují přístup k IoT Hubu podle funkcí.

* **RegistryRead**. Udělí přístup pro čtení k registru identit. Další informace najdete v části [registr identit](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **RegistryReadWrite**. Udělí registru identity přístup pro čtení a zápis. Další informace najdete v části [registr identit](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **ServiceConnect**. Uděluje přístup k koncovým bodům pro komunikaci a monitorování cloudových služeb. Například uděluje oprávnění back-endu Cloud Services pro příjem zpráv ze zařízení do cloudu, posílání zpráv z cloudu na zařízení a načítání odpovídajících potvrzení o doručení.

* **DeviceConnect**. Udělí přístup k koncovým bodům orientovaným na zařízení. Například uděluje oprávnění odesílat zprávy ze zařízení do cloudu a přijímat zprávy z cloudu na zařízení. Tato oprávnění používají zařízení.

Existují dva způsoby, jak získat **DeviceConnect** oprávnění pomocí IoT Hub s [tokeny zabezpečení](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app): pomocí klíče identity zařízení nebo sdíleného přístupového klíče. Kromě toho je důležité si uvědomit, že všechny funkce dostupné ze zařízení jsou zpřístupněny návrhem u koncových bodů s předponou `/devices/{deviceId}` .

[Součásti služby mohou generovat pouze tokeny zabezpečení](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components) pomocí zásad sdíleného přístupu udělujících příslušná oprávnění.

Azure IoT Hub a další služby, které mohou být součástí řešení, umožňují správu uživatelů pomocí Azure Active Directory.

Data ingestovaná službou Azure IoT Hub můžou využívat nejrůznější služby, jako je Azure Stream Analytics a Azure Blob Storage. Tyto služby umožňují přístup pro správu. Přečtěte si další informace o těchto službách a dostupných možnostech:

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): škálovatelná, plně indexovaná databázová služba pro částečně strukturovaná data, která spravují metadata pro zařízení, která zřizujete, například atributy, konfiguraci a vlastnosti zabezpečení. Azure Cosmos DB nabízí vysoce výkonné zpracování a vysokou propustnost, schéma – nezávislá indexování dat a bohatou rozhraní SQL dotazů.

* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/): zpracování datových proudů v reálném čase v cloudu, které vám umožní rychle vyvíjet a nasazovat řešení s nízkými náklady, abyste mohli získat přehled o zařízeních, senzorech, infrastruktuře a aplikacích v reálném čase. Data z této plně spravované služby se můžou škálovat na libovolný svazek a přitom pořád dosahovat vysoké propustnosti, nízké latence a odolnosti.

* [Azure App Services](https://azure.microsoft.com/services/app-service/): cloudová platforma pro vytváření výkonných webových a mobilních aplikací, které se připojují k datům kdekoli; v cloudu nebo v místním prostředí. Vytvářejte poutavé mobilní aplikace pro iOS, Android a Windows. Integraci s vaším softwarem jako služby (SaaS) a podnikovými aplikacemi s okamžitým připojením k desítkám cloudových služeb a podnikových aplikací. Kód ve vašem oblíbeném jazyce a integrovaném vývojovém prostředí (.NET, Node.js, PHP, Python nebo Java) vám umožní vytvářet webové aplikace a rozhraní API rychleji než kdy dřív.

* [Logic Apps](https://azure.microsoft.com/services/app-service/logic/): funkce Logic Apps Azure App Service pomáhá integrovat vaše řešení IoT do stávajících obchodních systémů a automatizovat pracovní postupy. Logic Apps umožňuje vývojářům navrhovat pracovní postupy, které začínají triggerem, a pak provádět řadu kroků – pravidla a akce, které používají výkonné konektory pro integraci s vašimi podnikovými procesy. Logic Apps nabízí předem dostupné možnosti připojení k rozsáhlému ekosystému SaaS, cloudových a místních aplikací.

* [Azure Blob Storage](https://azure.microsoft.com/services/storage/): spolehlivé a ekonomické úložiště v cloudu pro data, která vaše zařízení odesílají do cloudu.

## <a name="conclusion"></a>Závěr

Tento článek poskytuje přehled podrobností úrovně implementace pro návrh a nasazení infrastruktury IoT pomocí Azure IoT. Konfigurace každé součásti tak, aby byla zabezpečená, je klíčem k zabezpečení celkové infrastruktury IoT. Volby návrhu dostupné v Azure IoT poskytují určitou úroveň flexibility a možnosti volby; Každá volba ale může mít vliv na zabezpečení. Doporučuje se, aby se každá z těchto možností vyhodnotila prostřednictvím vyhodnocení rizika a nákladů.
