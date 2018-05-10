---
title: zahrnout soubor
description: zahrnout soubor
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 7d7cd8a197a89781a75f47bb4b4e2ec8fe7c3cb4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="secure-your-iot-deployment"></a>Zabezpečení nasazení IoT

Tento článek poskytuje další úroveň podrobností pro zabezpečení infrastruktury založené na Azure IoT Internet věcí (IoT). Odkazuje úrovně podrobnosti implementace pro konfiguraci a nasazení jednotlivých součástí. Poskytuje taky porovnání a možnosti mezi různé konkurenční metody.

Zabezpečení Azure IoT nasazení je možné rozdělit do těchto tří zabezpečení oblastí:

* **Zabezpečení zařízení**: při nasazení v zástupné zabezpečení zařízení IoT.
* **Zabezpečení připojení**: zajištění všechna data přenášená mezi zařízení IoT a IoT Hub je důvěrný a manipulací.
* **Zabezpečení cloudu**: poskytnutím prostředků k zabezpečení dat, zatímco prochází přes a je uložen v cloudu.

![Tři oblasti zabezpečení][img-overview]

## <a name="secure-device-provisioning-and-authentication"></a>Zabezpečené zřizování zařízení a ověřování

Akcelerátorů řešení IoT zabezpečit zařízení IoT pomocí následujících dvou metod:

* Tím, že pro každé zařízení, která umožňuje zařízením komunikovat s centrem IoT poskytuje jedinečnou identitu klíč (tokeny zabezpečení).
* Pomocí na zařízení [certifikát X.509] [ lnk-x509] a privátní klíč jako prostředek k ověření zařízení do služby IoT Hub. Tato metoda ověřování zajišťuje, že privátní klíč v zařízení není znám mimo zařízení kdykoli, poskytuje vyšší úroveň zabezpečení.

V případě metody token zabezpečení poskytuje ověření pro každé volání zařízení do služby IoT Hub tím, že přidružíte symetrický klíč pro každé volání. Ověřování na základě X.509 umožňuje ověřování zařízení IoT ve fyzické vrstvě jako součást navázání připojení protokol TLS. Bez ověřování X.509, což je méně bezpečné vzor lze metodu na základě zabezpečení token. Volba mezi tyto dvě metody je primárně určen míry budou zabezpečené zařízení ověřování musí být a dostupnost zabezpečeného úložiště v zařízení (bezpečně uložit privátní klíč).

## <a name="iot-hub-security-tokens"></a>Tokeny zabezpečení IoT Hub

IoT Hub používá tokeny zabezpečení k ověřování zařízení a služby se odesílání klíčů v síti. Kromě toho mají omezenou dobu platnosti a obor tokeny zabezpečení. Sady SDK služby Azure IoT automaticky generovat tokeny bez nutnosti žádnou zvláštní konfiguraci. Některé scénáře, ale vyžadují uživatele pro vygenerování a použití tokenů zabezpečení přímo. Mezi tyto scénáře patří přímého použití MQTT, AMQP nebo HTTP ploch nebo implementace vzoru služby tokenů.

Další informace o struktuře token zabezpečení a jeho použití naleznete v následujících článcích:

* [Struktura tokenu zabezpečení][lnk-security-tokens]
* [Pomocí SAS tokeny jako zařízení][lnk-sas-tokens]

Každé centrum IoT má [registru identit] [ lnk-identity-registry] které lze použít k vytvoření prostředků na zařízení v rámci služby, jako je například fronty, který obsahuje neukládají zprávy typu cloud zařízení a k povolení přístupu k zařízení přístupem koncových bodů. Registr identit služby IoT Hub poskytuje zabezpečené úložiště identit zařízení a zabezpečení klíčů pro řešení. Jednotlivé nebo skupiny identit zařízení lze přidat na seznam povolených nebo blokovaných, povolení plnou kontrolu nad přístup k zařízení. Následující články poskytují další informace o struktuře registru identit a podporované operace.

[IoT Hub podporuje protokoly, například MQTT, AMQP a HTTP][lnk-protocols]. Každý z těchto protokolů jinak používá tokeny zabezpečení ze zařízení IoT do služby IoT Hub:

* AMQP: SASL prostý a založené na deklaracích AMQP zabezpečení (`{policyName}@sas.root.{iothubName}` s tokeny úrovni centra IoT; `{deviceId}` s tokeny obor zařízení).
* MQTT: Připojení paketu používá `{deviceId}` jako `{ClientId}`, `{IoThubhostname}/{deviceId}` v **uživatelské jméno** pole a SAS token v **heslo** pole.
* HTTP: Je platný token v hlavičce autorizace požadavku.

Registr identit služby IoT Hub lze použít ke konfiguraci zařízení zabezpečovací přihlašovací údaje a řízení přístupu. Však řešení IoT již má významné investice v [vlastní zařízení identity registru nebo ověřování schématu][lnk-custom-auth], lze ji integrovat do existující infrastruktury s centrem IoT vytvořením služby tokenů.

### <a name="x509-certificate-based-device-authentication"></a>Ověřování zařízení na základě certifikátu X.509

Použití [zařízení na základě certifikátu X.509] [ lnk-use-x509] a jeho přidružený privátní a veřejné klíče dvojice povoluje další ověřování ve fyzické vrstvě. Privátní klíč je bezpečně uloženo na zařízení a není zjistitelný mimo zařízení. Certifikát X.509 obsahuje informace o zařízení, jako je například ID zařízení a další podrobnosti organizace. Podpis certifikátu je vytvořen pomocí soukromého klíče.

Zřizování toku vysoké úrovně zařízení:

* Přidružte identifikátor fyzického zařízení – identitu zařízení a/nebo certifikát X.509 přidružené k zařízení během zařízení výrobní nebo uvedení do provozu.
* Vytvořte záznam odpovídající identity ve IoT Hub – identitu zařízení a informace o přidružených zařízení v registru identit služby IoT Hub.
* Kryptografický otisk certifikátu X.509 bezpečně uložte v registru identit služby IoT Hub.

### <a name="root-certificate-on-device"></a>Kořenový certifikát na zařízení

Při navazování připojení TLS zabezpečené službou IoT Hub, ověřuje zařízení IoT pomocí kořenový certifikát, který je součástí sady SDK zařízení IoT Hub. Pro klienta nástroje C sady SDK, certifikát se nachází ve složce "\\c\\certifikátů" v kořenovém úložišti. I když tyto kořenových certifikátů je dlouhodobé, jsou stále může vyprší nebo odvolat. Pokud neexistuje žádný způsob aktualizace certifikát v zařízení, nemusí být zařízení následně připojit ke službě IoT Hub (nebo jiné cloudové služby). Toto riziko s znamená aktualizovat kořenový certifikát, když je zařízení IoT efektivně nasazených snižuje.

## <a name="securing-the-connection"></a>Zabezpečení připojení

Připojení k Internetu mezi zařízení IoT a IoT Hub, je zabezpečena pomocí standardní zabezpečení TLS (Transport Layer). Azure IoT podporuje [TLS 1.2][lnk-tls12], TLS 1.1 a TLS 1.0, v tomto pořadí. Podpora pro protokol TLS 1.0 je k dispozici pouze z důvodů zpětné kompatibility. Pokud je to možné používejte TLS 1.2 jako poskytuje nejvyšší zabezpečení.

## <a name="securing-the-cloud"></a>Zabezpečení cloudu

Azure IoT Hub umožňuje definice [zásad řízení přístupu] [ lnk-protocols] pro každý klíč zabezpečení. Následující sadu oprávnění, používá k udělení přístupu k všechny koncové body centra IoT. Oprávnění omezit přístup do služby IoT Hub, v závislosti na funkcích.

* **RegistryRead**. Uděluje přístup do registru identit pro čtení. Další informace najdete v tématu [registru identit][lnk-identity-registry].
* **RegistryReadWrite**. Uděluje přístup čtení a zápisu do registru identit. Další informace najdete v tématu [registru identit][lnk-identity-registry].
* **ServiceConnect**. Uděluje přístup do cloudu komunikace a sledování koncových bodů služby přístupem. Například uděluje oprávnění k back-end cloudové služby na příjem zpráv typu zařízení cloud, odesílání zpráv typu cloud zařízení a načíst odpovídající potvrzování doručení.
* **DeviceConnect**. Uděluje přístup k zařízení přístupem koncových bodů. Například uděluje oprávnění k odesílání zpráv typu zařízení cloud a příjem zpráv typu cloud zařízení. Toto oprávnění je používán zařízení.

Existují dva způsoby, jak získat **DeviceConnect** oprávnění službou IoT Hub s [tokeny zabezpečení][lnk-sas-tokens]: pomocí klíče identity zařízení nebo sdílený přístupový klíč. Kromě toho je důležité si uvědomit, že všechny funkce, které jsou přístupné ze zařízení je zveřejněný prostřednictvím návrhu na koncové body s předponou `/devices/{deviceId}`.

[Součásti služby můžete generovat jenom tokeny zabezpečení] [ lnk-service-tokens] pomocí sdílené zásady přístupu udělení příslušných oprávnění.

Azure IoT Hub a dalším službám, které může být součástí řešení povolit správu uživatelů pomocí služby Azure Active Directory.

Data ve službě Azure IoT Hub požity mohou být spotřebovávána různých služeb, jako je Azure Stream Analytics a úložiště objektů blob Azure. Tyto služby umožňují přístup pro správu. Další informace o těchto službách a dostupné možnosti:

* [Azure Cosmos DB][lnk-cosmosdb]: škálovatelné a plně indexované databázová služba pro částečně strukturovaných dat, který spravuje metadata pro zařízení, zřídíte, jako je například atributy, konfiguraci a vlastnosti zabezpečení. Azure Cosmos DB nabízí vysoce výkonné a vysokou propustností zpracování, bez ohledu na schéma indexování dat a bohaté rozhraní SQL.
* [Azure Stream Analytics][lnk-asa]: zpracování v cloudu, která umožňuje rychle vyvíjet a nasadit řešení analytics nízkonákladové k odhalení přehledy v reálném čase ze zařízení, senzorů, infrastruktura, streamu v reálném čase a aplikace. Data z této plně spravovanou službu, můžete škálovat na jakýkoli svazek, zatímco stále dosahuje vysoké propustnosti, s nízkou latencí a odolnost proti chybám.
* [Azure App Services][lnk-appservices]: Cloudová platforma vytvářet výkonné webové a mobilní aplikace, které se připojují k datům odkudkoli; v cloudu nebo místně. Vytvářejte poutavé mobilní aplikace pro iOS, Android a Windows. Integrate váš Software jako služba (SaaS) a podnikové aplikace s připojením se na pole k desítek cloudové služby a podnikové aplikace. Kód na váš oblíbený jazyk a IDE (.NET, Node.js, PHP, Python nebo Java) k vytvoření webové aplikace a rozhraní API rychleji než kdy dřív.
* [Služba Logic Apps][lnk-logicapps]: funkce The Logic Apps služby Azure App Service pomáhá integrovat řešení IoT tak, aby vaše stávající-obchodní systémy a automatizovat pracovní postupy. Služba Logic Apps umožňuje vývojářům navrhovat pracovní postupy, které začínají spouštěčem událostí a provádějí sérii kroků – pravidla a akce, které použít Výkonné konektory pro integraci s procesy vaší firmy. Služba Logic Apps nabízí připojení se na pole k velká ekosystém SaaS, cloudových a místních aplikací.
* [Úložiště objektů blob Azure][lnk-blob]: spolehlivé a ekonomické cloudové úložiště pro data, která vaše zařízení odesílají do cloudu.

## <a name="conclusion"></a>Závěr

Tento článek obsahuje přehled implementace úroveň podrobností pro navrhování a nasazení infrastruktury IoT pomocí Azure IoT. Konfigurace jednotlivých součástí zabezpečená je klíč v zabezpečení celkové infrastruktury IoT. K dispozici v Azure IoT volbách návrhu zadejte určité úrovně flexibilitu a možnost výběru; Každý výběr však může mít vliv na zabezpečení. Doporučuje se každý z těchto možností vyhodnotí vyhodnoťte riziko/náklady.

[img-overview]: media/iot-secure-your-deployment/overview.png

[lnk-security-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure
[lnk-sas-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-identity-registry]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-protocols]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-custom-auth]: ../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication
[lnk-x509]: http://www.itu.int/rec/T-REC-X.509-201210-I/en
[lnk-use-x509]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-tls12]: https://tools.ietf.org/html/rfc5246
[lnk-service-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components
[lnk-cosmosdb]: https://azure.microsoft.com/services/cosmos-db/
[lnk-asa]: https://azure.microsoft.com/services/stream-analytics/
[lnk-appservices]: https://azure.microsoft.com/services/app-service/
[lnk-logicapps]: https://azure.microsoft.com/services/app-service/logic/
[lnk-blob]: https://azure.microsoft.com/services/storage/
