---
title: "Ochrana osobních údajů v přenosu pomocí šifrování v Azure | Microsoft Docs"
description: "informace o Azure šifrování na ochranu osobních údajů může být užitečné ve snaze o souladu s obecné Data Protection nařízení (GDPR)."
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 6975358d40206a497a53de16731d16ef374db905
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="azure-encryption-technologies-protect-personal-data-in-transit-with-encryption"></a>Azure šifrovací technologie: ochrana osobních údajů v přenosu s šifrováním

Tento článek vám pomůže pochopit a používat technologie Azure šifrování k zabezpečení dat při přenosu. Ochrana osobních údajů osobních údajů při přenosu v síti je důležitou součástí strategie zabezpečení víceúrovňová obrany zabezpečení. Šifrování během přenosu je navržená tak, aby útočník, který zabrání přenosů nebudou moci zobrazit nebo použít data. Informace obsažené v tomto článku může být užitečné při organizaci ve snaze o souladu s obecné Data Protection nařízení (GDPR).

## <a name="scenario"></a>Scénář

Velké výletních společnosti, centrálou ve Spojených státech amerických, je rozšířit jeho operací a nabídnout itineráře v Středomoří, Jaderského a baltský moři, jakož i Britské ostrovy. Pro podporu těchto úsilí, získala menší výletních Víceřádkový na základě v Itálii, Německo, Dánsko a Spojeném království 

Společnost používá Microsoft Azure k ukládání firemních dat v cloudu. To zahrnuje osobní identifikovatelné údaje, například jména, adresy, telefonních čísel a informace o kreditní kartě z jeho základní globální zákazníka. Ve všech umístěních zahrnuje také tradiční informace lidských zdrojů, jako je adresy, telefonních čísel, daň identifikační čísla a další informace o zaměstnance společnosti. Na řádku výletních také udržuje velké databáze potřebu a věrnost program členů, která zahrnuje osobní údaje ke sledování vztahů se zákazníky aktuální a starší.

Osobní údaje zákazníků je zadána v databázi ze vzdálených pobočkách společnosti a z agentů cesta umístěné po celém světě. Dokumenty, které obsahují informace o zákazníkovi se přenáší přes síť do úložiště Azure.

## <a name="problem-statement"></a>Popis problému

Společnosti musí ochrany osobních údajů zaměstnanců a zákazníků osobních údajů, i když je při přenosu do a ze služby Azure.

## <a name="company-goal"></a>Cílem společnosti

Cílem společnosti zajistit, že osobní data se šifrují při vypnutí disku. Pokud neoprávněné osoby intercept vypnout disku osobní data, musí být ve formuláři, který bude zobrazovat nečitelná. Použití šifrování by měla být snadná nebo zcela transparentní, uživatelům a správcům.

## <a name="solutions"></a>Řešení

Azure services poskytují několik nástrojů a technologie, které vám umožní chránit osobní údaje při přenosu.

### <a name="azure-storage"></a>Azure Storage

Data, která je uložená v cloudu musíte se dostavit z klienta, které můžou být fyzicky umístěná kdekoliv na světě, do datového centra Azure. Pokud tato data jsou načítána pro uživatele, přenáší se znovu, v opačném směru. Data, která je při přenosu prostřednictvím veřejného Internetu je vždy hrozí zachycení útočníků. Je důležité chránit osobní údaje osobních údajů s využitím šifrování transportní vrstvy je zabezpečit při jejich přesunu mezi umístěními.

Protokol HTTPS poskytuje zabezpečené, šifrované komunikační kanál přes Internet. HTTPS slouží k přístupu k objektům ve službě Azure Storage a při volání rozhraní REST API. Vynutit používání protokolu HTTPS, při použití [sdílené přístupové podpisy](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) (SAS) delegovat přístup k objektům Azure Storage. Existují dva typy SAS: SAS služby a SAS účtu.

#### <a name="how-do-i-construct-a-service-sas"></a>Jak vytvořit SAS služby?

SAS služby deleguje přístup k prostředku jen v jedné službě úložiště (služba objektů blob, fronty, tabulka nebo souboru). K vytvoření služby SAS, postupujte takto:

1. Zadejte pole podepsaný verze

2. Zadejte podepsaných prostředků (objektů Blob a pouze služba souborů)

3. Zadejte parametry dotazu k přepsání hlavičky odpovědi (služby objektů Blob a pouze služba souborů)

4. Zadejte název tabulky (pouze služby Table)

5. Zadejte zásady přístupu

6. Zadejte Interval platnosti podpisu

8. Zadejte oprávnění

9. Zadejte IP adresu nebo rozsah IP adres

10. Zadejte protokol HTTP

11. Zadejte rozsahy přístup tabulky

12. Zadejte identifikátor podepsané

13. Zadejte podpis

Další podrobné pokyny naleznete v tématu [vytváření SAS služby](https://docs.microsoft.com/rest/api/storageservices/Constructing-a-Service-SAS?redirectedfrom=MSDN).

#### <a name="how-do-i-construct-an-account-sas"></a>Jak vytvořit SAS účtu?

SAS účtu deleguje přístup k prostředkům v jedné nebo více službách úložiště. Můžete taky delegovat přístup k operacím čtení, zápis a odstranění pro kontejnery objektů blob, tabulky a sdílené složky, který se nedá vymezit přes SAS služby. Konstrukce SAS účtu je podobná SAS služby. Podrobné pokyny najdete v tématu [vytváření SAS účtu.](https://docs.microsoft.com/rest/api/storageservices/Constructing-an-Account-SAS?redirectedfrom=MSDN)

#### <a name="how-do-i-enforce-https-when-calling-rest-apis"></a>Jak se při volání rozhraní REST API vynutit HTTPS?

Pokud chcete vynutit použití protokolu HTTPS, při volání rozhraní REST API pro přístup k objektům v účtech úložiště, můžete povolit zabezpečení přenosu požadované pro účet úložiště. 

1. Na portálu Azure vyberte **vytvořit účet úložiště**, nebo pro stávající účet úložiště, vyberte **nastavení** a potom **konfigurace**.

2. V části **zabezpečení přenosu požadované**, vyberte **povoleno**.

![Vytvoření účtu úložiště](media/protect-personal-data-in-transit-encryption/create-storage-account.png)

Podrobné pokyny, včetně toho, jak povolit zabezpečení přenosu požadované prostřednictvím kódu programu, najdete v článku [vyžadují zabezpečení přenosu](https://docs.microsoft.com/azure/storage/storage-require-secure-transfer).

#### <a name="how-do-i-encrypt-data-in-azure-file-storage"></a>Jak šifrují data v Azure File Storage?

K šifrování dat během přenosu s [Azure File Storage](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-portal), můžete použít protokol SMB 3.x s Windows 10, 8 a 8.1 a Windows Server 2012 R2 a Windows Server 2016. Pokud používáte službu Azure soubory, žádné připojení bez šifrování se nezdaří, pokud "Zabezpečení přenosu požadované" je povolena. To zahrnuje scénáře s využitím protokolu SMB 2.1, protokolu SMB 3.0 bez šifrování a některých typů klient Linux SMB.

#### <a name="azure-client-side-encryption"></a>Azure šifrování na straně klienta

Další možnost ochrany osobních údajů při přenášena mezi klientská aplikace a Azure Storage je [šifrování na straně klienta](https://docs.microsoft.com/azure/storage/storage-client-side-encryption). Data se zašifrují před přenášením do úložiště Azure a při načítání dat z Azure Storage, data se dešifrují po přijetí na straně klienta.

### <a name="azure-site-to-site-vpn"></a>Azure Site-to-Site VPN

Efektivní způsob, jak chránit osobní údaje při přenosu mezi podnikovou sítí nebo uživatele a virtuální síť Azure má používat [site-to-site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) nebo [point-to-site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) virtuální privátní sítě (VPN). Připojení k síti VPN vytvoří zabezpečené tunelové propojení šifrované přes Internet.

#### <a name="how-do-i-create-a-site-to-site-vpn-connection"></a>Vytvoření připojení site-to-site VPN

Síť site-to-site VPN připojí více uživatelů v podnikové síti do Azure. Pokud chcete vytvořit připojení site-to-site na portálu Azure, postupujte takto:

1. Vytvořte virtuální síť.

2. Určení serveru DNS.

3. Vytvořte podsíť brány.

4. Vytvoření brány VPN. 

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-01.png)

5. Vytvořte bránu místní sítě.

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-02.png)

6. Konfigurace zařízení VPN.

7. Vytvoření připojení k síti VPN.

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-03.png)

8. Ověření připojení VPN.

Podrobnější pokyny o tom, jak vytvořit připojení site-to-site na portálu Azure najdete v tématu [vytvořte připojení Site-to-Site na portálu Azure.] (https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

#### <a name="how-do-i-create-a-point-to-site-vpn-connection"></a>Vytvoření připojení VPN typu point-to-site?

Síť VPN Point-to-Site vytvoří zabezpečené připojení z jednotlivých klientských počítačů k virtuální síti. To je užitečné, pokud chcete připojit k Azure ze vzdáleného umístění, například z domova nebo hotelů nebo konference center. Na portálu Azure vytvoříte připojení typu point-to-site

1. Vytvořte virtuální síť.

2. Přidáte podsíť brány.

3. Určení serveru DNS. (volitelné)

4. Vytvoření brány virtuální sítě.

5. Generovat certifikáty.

6. Přidejte fond adres klienta.

7. Nahrajte data certifikátu veřejného kořenového certifikátu.

8. Generování a nainstalujte balíček konfigurace klienta VPN.

9. Nainstalujte certifikát exportovaného klienta.

10. Připojte k Azure.

11. Ověřte své propojení.

Další podrobné pokyny naleznete v tématu [konfigurace připojení typu Point-to-Site k virtuální síti ověřování pomocí certifikátů: portál Azure.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)

### <a name="ssltls"></a>SSL/TLS

Společnost Microsoft doporučuje vždy používat protokoly SSL/TLS pro výměnu dat v různých umístěních. Organizace, které se rozhodnete použít [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) přesunout velké sady dat přes síť WAN vyhrazené vysokorychlostní můžete odkaz taky šifrování dat na úrovni aplikace pomocí protokolu SSL/TLS nebo jiné protokoly pro přidání ochrany.

### <a name="encryption-by-default"></a>Ve výchozím nastavení šifrování

Společnost Microsoft používá šifrování k ochraně dat během přenosu mezi zákazníky a cloudové služby Azure. Pokud jsou interakci s Azure Storage prostřednictvím portálu Azure, všechny transakce dojít přes HTTPS.

[Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) je protokol, který datových Center společnosti Microsoft se pokusí o vyjednávání s klientské systémy, které se připojují ke cloudovým službám Microsoftu. Protokol TLS poskytuje silné ověřování, ochrana soukromí zpráv a integritu (umožňuje detekovat zpráva manipulaci, zachycení a padělání), vzájemná funkční spolupráce, flexibilita algoritmu, snadné nasazení a používání.

[Ideální Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) také zaměstnání tak, aby každé připojení mezi systémy klienta zákazníků a cloudových služeb Microsoftu používat jedinečné klíče. Připojení ke cloudovým službám Microsoftu taky využít výhod šifrování RSA na základě 2 048 bitů délky klíčů. Kombinace protokolu TLS, délky klíčů RSA 2 048 bitů a PFS usnadňuje mnohem obtížnější někdo k zachycení a přístup data, která je při přenosu mezi cloudové služby společnosti Microsoft a zákazníků.

Přenášená data zašifrována vždy v [Data Lake Store] (https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview). Kromě šifrování dat před uložením na trvalé médium se také vždy šifrují přenášená data pomocí protokolu HTTPS. HTTPS je jediný protokol, který se podporuje v rozhraních REST služby Data Lake Store.

## <a name="summary"></a>Souhrn

Společnost můžete provést jeho cílem ochrany osobních údajů a ochrana osobních údajů těchto údajů vynucování připojení prostřednictvím protokolu HTTPS do služby Azure Storage, pomocí sdílené přístupové podpisy a povolení zabezpečení přenosu vyžaduje na účty úložiště. Pomocí připojení protokolu SMB 3.0 a implementaci šifrování na straně klienta taky chránit osobní údaje. Připojení Site-to-site VPN z podnikové sítě pro virtuální síť Azure a připojení VPN typu point-to-site z jednotlivých uživatelů vytvoří zabezpečené tunelové propojení, pomocí kterého můžete bezpečně cestují osobní data. Postupy šifrování výchozí společnosti Microsoft bude další ochrany osobních údajů osobních údajů.

## <a name="next-steps"></a>Další postup

- [Doporučené postupy zabezpečení služby Azure Data a šifrování](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices)

- [Plánování a návrh pro VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

- [VPN Gateway – nejčastější dotazy](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq)

- [Zakoupení a konfigurace certifikátu protokolu SSL pro Azure App Service](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)
