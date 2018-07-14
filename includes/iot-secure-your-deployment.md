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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38943543"
---
# <a name="secure-your-iot-deployment"></a>Zabezpečení nasazení IoT

Tento článek poskytuje další úroveň podrobností pro zabezpečení infrastruktury založené na Azure IoT Internet of Things (IoT). Odkazuje na úroveň podrobností implementace pro konfiguraci a zavádění jednotlivých komponent. Také poskytuje porovnávání s rozlišením a se nemusíte rozhodovat mezi různé konkurenční metody.

Zabezpečení nasazení Azure IoT je možné rozdělit do těchto tří zabezpečení oblastí:

* **Zabezpečení zařízení**: zabezpečení zařízení IoT, když je nasazena v reálném světě.
* **Zabezpečení připojení**: zajištění všechna data přenášená mezi zařízení IoT a centrem IoT je důvěrný a odolný proti manipulaci.
* **Cloud Security**: poskytnout způsob jejich zabezpečení dat prochází přes, a je uložen v cloudu.

![Tři oblasti zabezpečení][img-overview]

## <a name="secure-device-provisioning-and-authentication"></a>Zabezpečené zřizování zařízení a ověřování

Akcelerátory řešení IoT zabezpečit zařízení IoT tím, že těchto dvou metod:

* Zadáním klíče jedinečné identity (tokeny zabezpečení) pro každé zařízení, která umožňuje zařízením komunikovat s centrem IoT.
* Na zařízení pomocí [certifikát X.509] [ lnk-x509] a privátního klíče jako prostředek k ověření zařízení do služby IoT Hub. Tato metoda ověřování zajišťuje, že privátní klíč v zařízení není znám mimo zařízení v okamžiku, poskytuje vyšší úroveň zabezpečení.

Případě metody token zabezpečení poskytuje ověřování pro každé volání prováděných zařízení do služby IoT Hub tím, že přidružíte ke každému volání symetrický klíč. Ověřování založené na X.509 umožňuje ověřování zařízení IoT ve fyzické vrstvě jako součást navazování připojení protokolu TLS. Metodu založenou na bezpečnostní token, je možné bez ověřování X.509, což je méně bezpečné vzor. Volba mezi těmito dvěma metodami je primárně určený zabezpečené zařízení ověřování musí být a dostupnost služby Zabezpečené úložiště v zařízení (bezpečně uložit privátní klíč).

## <a name="iot-hub-security-tokens"></a>Tokeny zabezpečení IoT Hub

IoT Hub používá tokeny zabezpečení k ověření zařízení a služeb se odesílání klíče v síti. Kromě toho mají omezenou dobu platnosti a rozsahu tokenů zabezpečení. Sady Azure IoT SDK automaticky generovat tokeny nevyžaduje žádnou zvláštní konfiguraci. Některé scénáře však vyžadují uživateli generovat a používat přímo tokeny zabezpečení. Mezi tyto scénáře patří přímému použití povrchy protokol MQTT, AMQP nebo HTTP, nebo k implementaci modelu služby tokenů.

Další informace o struktuře tokenu zabezpečení a jejich využití najdete v následujících článcích:

* [Struktura tokenu zabezpečení][lnk-security-tokens]
* [Použití tokenů SAS jako zařízení][lnk-sas-tokens]

Každé centrum IoT má [registr identit] [ lnk-identity-registry] , který slouží k vytváření prostředků na zařízení ve službě, jako jsou fronty, která obsahuje neukládají žádné zprávy typu cloud zařízení a povolit přístup k Koncové body přístupem k zařízení. Registr identit služby IoT Hub poskytuje zabezpečené úložiště identit zařízení a zabezpečení klíčů pro řešení. Osoby nebo skupiny identit zařízení můžete přidat na seznam povolených nebo blokovaných webů, umožňuje úplnou kontrolu přístupu zařízení. Následující články poskytují další informace o struktuře registru identit a podporované operace.

[IoT Hub podporuje protokoly, například MQTT, AMQP a HTTP][lnk-protocols]. Každá z těchto protokolů jinak používá tokeny zabezpečení IoT zařízení do služby IoT Hub:

* AMQP: SASL prostý a založené na deklaracích AMQP zabezpečení (`{policyName}@sas.root.{iothubName}` s IoT hub úrovně tokeny; `{deviceId}` s rozsahem zařízení tokeny).
* MQTT: Připojení používá paketů `{deviceId}` jako `{ClientId}`, `{IoThubhostname}/{deviceId}` v **uživatelské jméno** pole a SAS token v **heslo** pole.
* HTTP: Je platný token v hlavičce autorizace požadavku.

Registr identit služby IoT Hub je možné konfigurovat jednotlivá zařízení zabezpečovacích přihlašovacích údajů a řízení přístupu. Nicméně pokud je řešení IoT už významnou investici [schéma registru a/nebo ověření identity vlastní zařízení][lnk-custom-auth], je možné integrovat do existující infrastruktury pomocí služby IoT Hub Po vytvoření služby tokenů.

### <a name="x509-certificate-based-device-authentication"></a>Ověřování pomocí certifikátu zařízení X.509

Použití [na základě zařízení certifikát X.509] [ lnk-use-x509] a jeho přidružený privátní a veřejné pár klíčů umožňuje dodatečné ověření ve fyzické vrstvě. Privátní klíč je bezpečně uložen v zařízení a není mimo zařízení zjistitelné. Certifikát X.509 obsahuje informace o zařízení, jako je například ID zařízení a další údaje o organizaci. Podpis certifikátu je generována pomocí privátního klíče.

Toku zřizování zařízení vysoké úrovně:

* Přidružte identifikátor na fyzické zařízení – identitu zařízení a/nebo certifikát X.509, které jsou přidružené k zařízení během výrobního nebo uvedení do provozu na zařízení.
* Vytvořte položku odpovídající identitu ve službě IoT Hub – identitu zařízení a informace o přidružené zařízení v registru identit služby IoT Hub.
* Bezpečné ukládání kryptografický otisk certifikátu X.509 v registru identit služby IoT Hub.

### <a name="root-certificate-on-device"></a>Kořenový certifikát na zařízení

Při navazování zabezpečeného připojení TLS službou IoT Hub, IoT zařízení ověřuje pomocí kořenového certifikátu, který je součástí sady SDK pro zařízení služby IoT Hub. C klientskou sadou SDK, certifikát se nachází ve složce "\\c\\certifikáty" v kořenovém adresáři úložiště. I když jsou dlouhodobé kořenové certifikáty, jsou stále může vypršet jejich platnost nebo odvolat. Pokud neexistuje žádný způsob aktualizace certifikátu na zařízení, zařízení nemusí být následně připojit k službě IoT Hub (nebo jiné cloudové služby). S prostředky aktualizovat kořenový certifikát po zařízení IoT nasazení efektivně zmírňuje rizika.

## <a name="securing-the-connection"></a>Zabezpečení připojení

Připojení k Internetu mezi zařízení IoT a centrem IoT je zabezpečený pomocí standardu zabezpečení TLS (Transport Layer). Azure IoT podporuje [TLS 1.2][lnk-tls12], TLS 1.1 a TLS 1.0, v uvedeném pořadí. Podpora protokolu TLS 1.0 se poskytuje pouze z důvodů zpětné kompatibility. Použití protokolu TLS 1.2, pokud je to možné, protože poskytuje nejvyšší zabezpečení.

## <a name="securing-the-cloud"></a>Zabezpečení cloudu

Azure IoT Hub umožňuje definici [zásad řízení přístupu] [ lnk-protocols] pro každý klíč zabezpečení. Používá následující sadu oprávnění pro udělení přístupu k jednotlivým koncové body IoT Hubu. Oprávnění omezují přístup do služby IoT Hub, v závislosti na funkcích.

* **RegistryRead**. Uděluje přístup pro čtení k registru identit. Další informace najdete v tématu [registr identit][lnk-identity-registry].
* **RegistryReadWrite**. Uděluje přístup čtení a zápis do registru identit. Další informace najdete v tématu [registr identit][lnk-identity-registry].
* **ServiceConnect**. Uděluje přístup ke cloudovým komunikace a monitorování koncových bodů služby přístupem. Například uděluje oprávnění k back endové cloudové služby pro příjem zpráv typu zařízení cloud, odesílat zprávy typu cloud zařízení a načtení odpovídajícího doručení potvrzení.
* **DeviceConnect**. Uděluje přístup ke koncovým bodům přístupem k zařízení. Například uděluje oprávnění k odesílání zpráv typu zařízení cloud a příjem zpráv typu cloud zařízení. Toto oprávnění se používají zařízení.

Existují dva způsoby, jak získat **DeviceConnect** oprávnění službou IoT Hub s [tokeny zabezpečení][lnk-sas-tokens]: klíč identity zařízení nebo sdílený přístupový klíč. Kromě toho je důležité si uvědomit, že všechny funkce, které jsou přístupné ze zařízení je zveřejněna rozhraním návrhu na koncové body s předponou `/devices/{deviceId}`.

[Součásti služby můžou jenom generovat tokeny zabezpečení] [ lnk-service-tokens] pomocí sdílené zásady přístupu k udělení příslušných oprávnění.

Azure IoT Hub a dalším službám, které mohou být součástí řešení povolit správu uživatelů pomocí Azure Active Directory.

Dat přijatý službou Azure IoT Hub můžete využívat širokou škálu služeb, jako je Azure Stream Analytics a Azure blob storage. Tyto služby umožňují přístup ke správě. Další informace o těchto služeb a dostupných možností:

* [Azure Cosmos DB][lnk-cosmosdb]: škálovatelná, plně indexované databázovou službu pro částečně strukturovaná data, která spravuje metadat pro zařízení si zřídíte, jako jsou atributy, konfigurace a vlastnosti zabezpečení. Azure Cosmos DB nabízí vysoce výkonné a vysoce propustné zpracování, schématu nezávislé indexování dat a také bohaté rozhraní příkazů jazyka SQL.
* [Azure Stream Analytics][lnk-asa]: v reálném čase v cloudu, která umožňuje rychlý vývoj a nasazení nízkonákladového analytického řešení pro odhalení informací v reálném čase ze zařízení, senzorů, infrastruktury, pro zpracování datových proudů a aplikace. Data z této plně spravované služby můžete škálovat pro jakýkoli svazek, zatímco stále dosahuje vysoké propustnosti, nízké latence a odolnost proti chybám.
* [Služba Azure App Services][lnk-appservices]: cloudovou platformou můžete tvořit výkonné webové a mobilní aplikace, které se připojují k datům bez ohledu na; v cloudu nebo místně. Vytvářejte poutavé mobilní aplikace pro iOS, Android a Windows. Integrate váš Software jako služba (SaaS) a podnikové aplikace s out-of-the-box připojení k desítkám cloudových služeb a podnikových aplikací. Kódování v oblíbeném jazyce a integrovaném vývojovém prostředí (.NET, Node.js, PHP, Python nebo Java) k vytváření webových aplikací a rozhraní API rychleji než kdy dřív.
* [Logic Apps][lnk-logicapps]: funkci Logic Apps služby Azure App Service pomáhá vašemu řešení IoT umožní vaše stávající systémy z podnikové integrace a automatizace pracovních postupů. Logic Apps umožňuje vývojářům navrhovat pracovní postupy, které začínají spouštěčem událostí a provádějí sérii kroků, pravidla a akce, které používají výkonné konektory pro integraci s obchodními procesy. Logic Apps nabízí out-of-the-box připojení k rozsáhlému ekosystému aplikací SaaS, cloudové a místní aplikace.
* [Úložiště objektů blob v Azure][lnk-blob]: spolehlivé, úsporné cloudové úložiště pro data, která vaše zařízení odesílají do cloudu.

## <a name="conclusion"></a>Závěr

Tento článek poskytuje přehled implementace úroveň podrobností pro navrhování a nasazení infrastruktury IoT pomocí Azure IoT. Konfigurace jednotlivých komponent zabezpečení je klíč při zabezpečení celkovou infrastrukturu IoT. Volby návrhu k dispozici ve službě Azure IoT poskytovat určitou úroveň Flexibilita a možnost volby; každou volbu však může mít vliv na zabezpečení. Doporučuje se, že každý z těchto možností vyhodnocen prostřednictvím posuzování rizik a náklady.

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
