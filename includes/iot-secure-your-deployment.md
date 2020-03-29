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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76748859"
---
Tento článek obsahuje další úroveň podrobností pro zabezpečení infrastruktury Internetu věcí (IoT) založené na Azure IoT. Odkazuje na podrobnosti na úrovni implementace pro konfiguraci a nasazení jednotlivých komponent. Poskytuje také srovnání a volby mezi různými konkurenčními metodami.

Zabezpečení nasazení Azure IoT lze rozdělit do následujících tří oblastí zabezpečení:

* **Zabezpečení zařízení:** Zabezpečení zařízení IoT, když je nasazeno ve volné přírodě.

* **Zabezpečení připojení**: Zajištění všech dat přenášených mezi zařízením IoT a ioT hubem je důvěrné a odolné proti manipulaci.

* **Cloud Security**: Poskytuje prostředky k zabezpečení dat, zatímco se pohybuje a je uložen v cloudu.

![Tři bezpečnostní oblasti](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Zřizování a ověřování zabezpečeného zařízení

Akcelerátory řešení IoT zabezpečují zařízení IoT pomocí následujících dvou metod:

* Poskytnutím jedinečného klíče identity (tokenů zabezpečení) pro každé zařízení, které může zařízení používat ke komunikaci s centrem IoT Hub.

* Pomocí [certifikátu X.509](https://www.itu.int/rec/T-REC-X.509-201210-S) na zařízení a soukromého klíče jako prostředku k ověření zařízení do služby IoT Hub. Tato metoda ověřování zajišťuje, že soukromý klíč v zařízení není mimo zařízení v žádném okamžiku znám, což poskytuje vyšší úroveň zabezpečení.

Metoda tokenu zabezpečení poskytuje ověřování pro každé volání zařízení do služby IoT Hub tím, že přizpůsobuje symetrický klíč ke každému volání. Ověřování založené na x.509 umožňuje ověřování zařízení IoT ve fyzické vrstvě jako součást připojení TLS. Metodu založenou na tokenech zabezpečení lze použít bez ověřování X.509, což je méně zabezpečený vzor. Volba mezi těmito dvěma metodami je primárně dána tím, jak bezpečné ověřování zařízení musí být, a dostupnost zabezpečeného úložiště v zařízení (bezpečně uložit soukromý klíč).

## <a name="iot-hub-security-tokens"></a>Tokeny zabezpečení centra IoT Hub

Služba IoT Hub používá tokeny zabezpečení k ověřování zařízení a služeb, aby se zabránilo odesílání klíčů v síti. Tokeny zabezpečení jsou navíc omezeny časovou platností a rozsahem. Sady Azure IoT SDK automaticky generují tokeny bez nutnosti jakékoli speciální konfigurace. Některé scénáře však vyžadují, aby uživatel přímo generoval a používal tokeny zabezpečení. Tyto scénáře zahrnují přímé použití mqtt, AMQP nebo HTTP povrchy nebo implementace vzoru služby tokenu.

Další podrobnosti o struktuře tokenu zabezpečení a jeho použití naleznete v následujících článcích:

* [Struktura tokenů zabezpečení](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [Použití tokenů SAS jako zařízení](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Každý IoT Hub má [registr identit,](../articles/iot-hub/iot-hub-devguide-identity-registry.md) který lze použít k vytvoření prostředků pro jednotlivé zařízení ve službě, jako je například fronta, která obsahuje zprávy z cloudu na zařízení za letu, a k povolení přístupu ke koncovým bodům směřujícím k zařízení. Registr identit služby IoT Hub poskytuje zabezpečené úložiště identit zařízení a klíčů zabezpečení pro řešení. Jednotlivé nebo skupiny identit zařízení lze přidat do seznamu povolených položek nebo seznamu blokování, což umožňuje úplnou kontrolu nad přístupem k zařízení. Následující články obsahují další podrobnosti o struktuře registru identit a podporovaných operací.

[IoT Hub podporuje protokoly, jako je MQTT, AMQP a HTTP](../articles//iot-hub/iot-hub-devguide-security.md). Každý z těchto protokolů používá tokeny zabezpečení ze zařízení IoT do ioT hubu jinak:

* AMQP: Zabezpečení založené na deklaracích SASL`{policyName}@sas.root.{iothubName}` PLAIN a AMQP (s tokeny na úrovni centra IoT; `{deviceId}` tokeny s rozsahem zařízení).

* MQTT: CONNECT `{deviceId}` paket `{ClientId}`používá `{IoThubhostname}/{deviceId}` jako , v poli **Uživatelské jméno** a token SAS v poli **Heslo.**

* HTTP: Platný token je v hlavičce žádosti o autorizaci.

Registr identit služby IoT Hub lze použít ke konfiguraci pověření zabezpečení pro zařízení a řízení přístupu. Pokud však řešení IoT již má významné investice do [vlastního registru identitzařízení nebo schématu ověřování](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication), lze jej integrovat do existující infrastruktury s službou IoT Hub vytvořením tokenové služby.

### <a name="x509-certificate-based-device-authentication"></a>Ověřování zařízení založené na certifikátu X.509

Použití [certifikátu X.509 založeného na zařízení](../articles/iot-hub/iot-hub-devguide-security.md) a jeho přidruženédvojice soukromého a veřejného klíče umožňuje další ověřování ve fyzické vrstvě. Soukromý klíč je bezpečně uložen v zařízení a není zjistitelný mimo zařízení. Certifikát X.509 obsahuje informace o zařízení, jako je ID zařízení a další podrobnosti o organizaci. Podpis certifikátu je generován pomocí soukromého klíče.

Tok zřizování zařízení na vysoké úrovni:

* Přidružte identifikátor k fyzickému zařízení – identita zařízení a/nebo certifikát X.509 přidružený k zařízení během výroby nebo uvedení zařízení do provozu.

* Vytvořte odpovídající položku identity v centru IoT Hub – identity zařízení a přidružené informace o zařízení v registru identit služby IoT Hub.

* Bezpečně uklánějte kryptografický otisk certifikátu X.509 do registru identit služby IoT Hub.

### <a name="root-certificate-on-device"></a>Kořenový certifikát na zařízení

Při navazování zabezpečeného připojení TLS pomocí služby IoT Hub ověřuje zařízení IoT Hub pomocí kořenového certifikátu, který je součástí sady SDK zařízení. Pro sadu SDK klienta C je\\certifikát\\umístěn pod složkou "c certs" pod kořenem úložiště. Ačkoli tyto kořenové certifikáty jsou dlouhodobé, stále mohou vypršet nebo být odvolány. Pokud neexistuje žádný způsob, jak aktualizovat certifikát na zařízení, zařízení nemusí být možné následně připojit k centru IoT Hub (nebo jiné cloudové služby). S prostředky k aktualizaci kořenového certifikátu po nasazení zařízení IoT účinně zmírňuje toto riziko.

## <a name="securing-the-connection"></a>Zabezpečení připojení

Připojení k Internetu mezi zařízením IoT a službou IoT Hub je zabezpečeno pomocí standardu TLS (Transport Layer Security). Azure IoT podporuje [TLS 1.2](https://tools.ietf.org/html/rfc5246), TLS 1.1 a TLS 1.0 v tomto pořadí. Podpora pro TLS 1.0 je k dispozici pouze pro zpětnou kompatibilitu. Zkontrolujte [podporu TLS v centru IoT,](../articles/iot-hub/iot-hub-tls-support.md) abyste zjistili, jak nakonfigurovat centrum tak, aby používalo TLS 1.2, protože poskytuje největší zabezpečení.

## <a name="securing-the-cloud"></a>Zabezpečení cloudu

Azure IoT Hub umožňuje definici [zásad řízení přístupu](../articles/iot-hub/iot-hub-devguide-security.md) pro každý klíč zabezpečení. Používá následující sadu oprávnění k udělení přístupu ke každému koncovému bodu ioT hubu. Oprávnění omezují přístup k IoT Hubu podle funkcí.

* **RegistryRead**. Uděluje přístup pro čtení do registru identit. Další informace naleznete v [registru identit](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **RegistryReadWrite**. Uděluje přístup pro čtení a zápis do registru identit. Další informace naleznete v [registru identit](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **ServiceConnect**. Uděluje přístup ke komunikaci cloudové služby a monitorování koncových bodů. Uděluje například oprávnění back-endovým cloudovým službám přijímat zprávy mezi zařízeními, odesílat zprávy z cloudu na zařízení a načítat odpovídající potvrzení o doručení.

* **DeviceConnect**. Uděluje přístup ke koncovým bodům směřujícím k zařízení. Uděluje například oprávnění k odesílání zpráv mezi zařízeními a přijímání zpráv z cloudu na zařízení. Toto oprávnění používají zařízení.

Existují dva způsoby, jak získat oprávnění **DeviceConnect** pomocí služby IoT Hub s [tokeny zabezpečení:](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)pomocí klíče identity zařízení nebo sdíleného přístupového klíče. Kromě toho je důležité si uvědomit, že všechny funkce přístupné ze zařízení `/devices/{deviceId}`jsou vystaveny podle návrhu na koncových bodech s předponou .

[Součásti služby mohou generovat tokeny zabezpečení pouze](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components) pomocí zásad sdíleného přístupu udělujících příslušná oprávnění.

Azure IoT Hub a další služby, které mohou být součástí řešení, umožňují správu uživatelů pomocí služby Azure Active Directory.

Data ingestovaná službou Azure IoT Hub můžou spotřebovávat celá řada služeb, jako je Azure Stream Analytics a úložiště objektů blob Azure. Tyto služby umožňují přístup ke správě. Přečtěte si více o těchto službách a dostupných možnostech:

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): Škálovatelná, plně indexovaná databázová služba pro částečně strukturovaná data, která spravují metadata pro zařízení, která zřídíte, jako jsou atributy, konfigurace a vlastnosti zabezpečení. Azure Cosmos DB nabízí vysoce výkonné a vysoce propustné zpracování, indexování dat bez schématu a bohaté rozhraní dotazu SQL.

* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/): Zpracování streamů v reálném čase v cloudu, které vám umožní rychle vyvinout a nasadit nízkonákladové analytické řešení pro odhalení přehledů v reálném čase ze zařízení, senzorů, infrastruktury a aplikací. Data z této plně spravované služby můžete škálovat na libovolný svazek a zároveň dosáhnout vysoké propustnosti, nízké latence a odolnosti proti chybám.

* [Azure App Services:](https://azure.microsoft.com/services/app-service/)Cloudová platforma pro vytváření výkonných webových a mobilních aplikací, které se připojují k datům kdekoli; v cloudu nebo v místním prostředí. Vytvářejte poutavé mobilní aplikace pro iOS, Android a Windows. Integrujte se svým softwarem jako službou (SaaS) a podnikovými aplikacemi s out-of-the-box připojení k desítkám cloudových služeb a podnikových aplikací. Kód ve vašem oblíbeném jazyce a IDE (.NET, Node.js, PHP, Python nebo Java) pro vytváření webových aplikací a rozhraní API rychleji než kdy předtím.

* [Logic Apps](https://azure.microsoft.com/services/app-service/logic/): Funkce Logic Apps služby Azure App Service pomáhá integrovat vaše řešení IoT do vašich stávajících obchodních systémů a automatizovat procesy pracovního postupu. Logic Apps umožňuje vývojářům navrhovat pracovní postupy, které začínají od aktivační události a pak provést řadu kroků – pravidla a akce, které používají výkonné konektory pro integraci s obchodními procesy. Logic Apps nabízí out-of-the-box připojení k rozsáhlé ekosystému SaaS, cloudové a místní aplikace.

* [Úložiště objektů blob Azure:](https://azure.microsoft.com/services/storage/)Spolehlivé a hospodárné cloudové úložiště pro data, která vaše zařízení odesílají do cloudu.

## <a name="conclusion"></a>Závěr

Tento článek obsahuje přehled podrobností o úrovni implementace pro navrhování a nasazování infrastruktury IoT pomocí Azure IoT. Konfigurace jednotlivých komponent tak, aby byla zabezpečená, je klíčem k zabezpečení celkové infrastruktury IoT. Možnosti návrhu, které jsou k dispozici v Azure IoT poskytují určitou úroveň flexibility a výběru; každá volba však může mít bezpečnostní důsledky. Doporučuje se, aby každá z těchto možností byla vyhodnocena prostřednictvím posouzení rizika/nákladů.
