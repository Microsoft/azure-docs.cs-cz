---
title: Funkce verze Preview Azure Firewall Premium
description: Azure Firewall Premium je spravovaná cloudová služba zabezpečení sítě, která chrání vaše prostředky Azure Virtual Network.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: 4a8efff7ef53753e15a47e87a2bb82d0124ae997
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590445"
---
# <a name="azure-firewall-premium-preview-features"></a>Funkce verze Preview Azure Firewall Premium

:::image type="content" source="media/premium-features/icsa-cert-firewall-small.png" alt-text="ICSA certifikace" border="false":::–:::image type="content" source="media/premium-features/pci-logo.png" alt-text="logo certifikace pro certifikaci PCI" border="false":::


> [!IMPORTANT]
> Azure Firewall Premium je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Azure Firewall Premium Preview je brána firewall nové generace s funkcemi, které jsou potřeba pro vysoce citlivá a regulovaná prostředí.

:::image type="content" source="media/premium-features/premium-overview.png" alt-text="Diagram přehledu Azure Firewall Premium":::

Azure Firewall Premium Preview používá zásady brány firewall, což je globální prostředek, který se dá použít k centrální správě bran firewall pomocí správce Azure Firewall Manager. Od této verze se všechny nové funkce dají konfigurovat jenom pomocí zásad brány firewall. Pravidla brány firewall (Classic) jsou nadále podporována a lze je použít ke konfiguraci existujících funkcí standardní brány firewall.  Zásady brány firewall je možné spravovat nezávisle nebo pomocí správce Azure Firewall. Zásady brány firewall spojené s jedinou bránou firewall jsou zdarma.

> [!IMPORTANT]
> V současné době nejsou v nasazeních zabezpečeného centra a v konfiguraci vynuceného tunelování dostupná SKU brány firewall Premium. 

Azure Firewall Premium Preview obsahuje následující funkce:

- **Kontrola TLS** – dešifruje odchozí přenosy, zpracovává data a pak data zašifruje a pošle je do cíle.
- **Zprostředkovatelů identity** – systém zjišťování a prevence vniknutí k síti (zprostředkovatelů identity) umožňuje sledovat síťové aktivity pro škodlivou aktivitu, protokolovat informace o této aktivitě, nahlásit je a volitelně se pokusit o její blokování.
- **Filtrování adresy URL** – rozšiřuje schopnost filtrování plně kvalifikovaného názvu domény Azure firewall, aby bylo možné zvážit celou adresu URL. Například `www.contoso.com/a/c` místo `www.contoso.com` .
- **Webové kategorie** – správci můžou povolit nebo odepřít přístup uživatelům k kategoriím webu, jako jsou weby hazardních her, weby sociálních médií a další.


## <a name="tls-inspection"></a>Kontrola TLS

Azure Firewall Premium ukončuje odchozí připojení a připojení k východnímu západnímu TLS. [Azure Application Gateway](../web-application-firewall/ag/ag-overview.md) podporuje příchozí kontrolu TLS, která umožňuje komplexní šifrování. Azure Firewall provede požadovaná hodnota zabezpečení a znovu zašifruje provoz odeslaný do původního cíle.

> [!TIP]
> TLS 1,0 a 1,1 jsou zastaralé a nepodporují se. Byly zjištěny hrozby TLS 1,0 a 1,1 TLS/SSL (Secure Sockets Layer) (SSL) a i když stále fungují, aby se zajistila zpětná kompatibilita, nejsou doporučováni. Migrujte na TLS 1,2 co nejrychleji.

Další informace o požadavcích na certifikát zprostředkující certifikační autority ve verzi Preview Azure Firewall Premium najdete v článku [certifikáty Azure firewall Premium Preview](premium-certificates.md).

## <a name="idps"></a>ZPROSTŘEDKOVATELŮ identity

Systém zjišťování a prevence vniknutí k síti (zprostředkovatelů identity) umožňuje monitorovat síť pro škodlivou aktivitu, protokolovat informace o této aktivitě, nahlásit je a volitelně se pokusit o její blokování. 

Azure Firewall Premium Preview poskytuje zprostředkovatelů IDENTITYům založenou na podpisech, které umožňují rychlou detekci útoků hledáním specifických vzorů, jako je například sekvence bajtů v síťovém provozu nebo známých škodlivých sekvencí instrukcí používaných malwarem. Signatury zprostředkovatelů identity jsou plně spravované a průběžné aktualizace.

Azure Firewall signatury/RuleSets zahrnují:
- Důraz na otisky prstů skutečného malwaru, příkazu a řízení, zneužití sad a v případě nebezpečných škodlivých aktivit vyvolaných tradičními metodami prevence.
- Více než 35 000 pravidel ve více než 50 kategoriích.
    - Mezi tyto kategorie patří příkazy a řízení malwaru, útoky DoS, botnety, informativní události, zneužití, chyby zabezpečení, síťové protokoly SCADA, aktivita zneužití sady a další.
- každý den se uvolní 20 až 40 dalších nových pravidel.
- Pozitivní hodnocení s nízkým falešnou hodnotou pomocí špičkového izolovaného prostoru pro malware a smyčky zpětné vazby ze sítě globálního senzoru.

ZPROSTŘEDKOVATELŮ identity umožňuje detekci útoků ve všech portech a protokolech pro nešifrovaný provoz. Když ale potřebujete zkontrolovat provoz HTTPS, Azure Firewall můžou použít jeho schopnost kontroly TLS k dešifrování provozu a lepší detekci škodlivých aktivit.  

Seznam obcházení zprostředkovatelů identity vám umožňuje Nefiltrovat provoz do žádné IP adresy, rozsahů a podsítí zadaných v seznamu pro obejití. 

## <a name="url-filtering"></a>Filtrování adres URL

Filtrování adres URL rozšiřuje schopnost filtrování plně kvalifikovaného názvu domény Azure Firewall, aby bylo možné zvážit celou adresu URL. Například `www.contoso.com/a/c` místo `www.contoso.com` .  

Filtrování adres URL lze použít jak u přenosu HTTP, tak i přes HTTPS. Po kontrole provozu pomocí protokolu HTTPS může Azure Firewall Premium Preview použít jeho schopnost kontroly TLS k dešifrování provozu a extrakci cílové adresy URL pro ověření, jestli je povolený přístup. Kontrola TLS vyžaduje výslovný souhlas na úrovni pravidla aplikace. Po povolení můžete použít adresy URL pro filtrování pomocí protokolu HTTPS. 

## <a name="web-categories"></a>Webové kategorie

Webové kategorie správcům umožňují povolit nebo odepřít přístup uživatelů k kategoriím webu, jako jsou například weby hazardních her, weby sociálních médií a další. Webové kategorie budou také zahrnuty ve Azure Firewall Standard, ale budou lépe vyladěny v Azure Firewall Premium Preview. Na rozdíl od možností kategorií webu ve standardní SKU, které odpovídají kategorii na základě plně kvalifikovaného názvu domény, skladová položka Premium odpovídá kategorii podle celé adresy URL pro přenos HTTP i HTTPS. 

Pokud například Azure Firewall zachytí požadavek HTTPS pro `www.google.com/news` , je očekávána následující kategorizace: 

- Firewall Standard – bude prověřena pouze část plně kvalifikovaného názvu domény, takže `www.google.com` bude zařazena do kategorie jako *vyhledávací modul*. 

- Firewall Premium – bude prověřena úplná adresa URL, takže `www.google.com/news` bude zařazena do kategorie *zpráv*.

Kategorie jsou seřazené na základě závažnosti v oblasti **zodpovědnosti**, **vysoké šířky pásma**, **používání firmy**, **ztráty produktivity**, **Obecné procházení** a **Nezařazeno do kategorie**.

### <a name="category-exceptions"></a>Výjimky kategorie

Můžete vytvořit výjimky pro pravidla vaší webové kategorie. Vytvořte samostatnou kolekci pravidel povolení nebo odepření s vyšší prioritou v rámci skupiny kolekce pravidel. Můžete například nakonfigurovat kolekci pravidel, která umožňuje `www.linkedin.com` s prioritou 100, s kolekcí pravidel, která zakazuje **sociální sítě** s prioritou 200. Tím se vytvoří výjimka pro předdefinovanou webovou kategorii **sítě pro sociální sítě** .

### <a name="categorization-change"></a>Změna kategorizace

Změnu kategorizace si můžete vyžádat v těchto případech:

 - Zamyslete se plně kvalifikovaný název domény nebo adresa URL v jiné kategorii. 
 
nebo 

- má navrhovanou kategorii pro plně kvalifikovaný název domény (Nezařazeno do kategorie) nebo adresu URL.

Vítá vás žádost o odeslání žádosti [https://aka.ms/azfw-webcategories-request](https://aka.ms/azfw-webcategories-request) .
 
## <a name="supported-regions"></a>Podporované oblasti

Verze Preview Azure Firewall Premium je podporována v následujících oblastech:

- Západní Evropa (Public/Evropa)
- Východní USA (Public/USA)
- Austrálie – východ (veřejná/Austrálie)
- Jihovýchodní Asie (veřejné/Asie a Tichomoří)
- Velká Británie – jih (veřejné/Spojené království)
- Severní Evropa (Public/Evropa)
- Východní USA 2 (Public/USA)
- Střed USA – jih (Public/USA)
- Západní USA 2 (Public/USA)
- Západní USA (Public/USA)
- Střed USA (Public/USA)
- Střed USA – sever (Public/USA)
- Japonsko – východ (veřejný/Japonsko)
- Východní Asie (Public/Asie a Tichomoří)
- Kanada – střed (veřejná/Kanada)
- Francie – střed (veřejná/Francie)
- Jihoafrická republika sever (veřejná nebo Jižní Afrika)
- Spojené arabské emiráty sever (Public/Spojené arabské emiráty)
- Švýcarsko – sever (veřejný/Švýcarsko)
- Brazílie – jih (veřejná/Brazílie)
- Norsko – východ (veřejná/Norsko)
- Austrálie – střed (veřejná/Austrálie)
- Austrálie – střed 2 (veřejná/Austrálie)
- Austrálie – jihovýchod (veřejná/Austrálie)
- Kanada – východ (veřejná/Kanada)
- Střed USA EUAP (veřejná/Kanárské (US))
- Francie – jih (veřejná/Francie)
- Japonsko – západ (veřejný/Japonsko)
- Korea – jih (veřejná/Korea)
- Spojené arabské emiráty – střed (Public/Spojené arabské emiráty)
- Velká Británie – západ (veřejné/Spojené království)
- Středozápadní USA (Public/USA)
- Západní Indie (Public/Indie)


## <a name="known-issues"></a>Známé problémy

Azure Firewall Premium Preview má následující známé problémy:

|Problém  |Description  |Omezení rizik  |
|---------|---------|---------|
|Kontrola TLS se podporuje jenom na portu Standard HTTPS.|Kontrola TLS podporuje jenom HTTPS/443. |Žádné Ostatní porty budou podporovány v GA.|
|Podpora ESNI pro rozlišení plně kvalifikovaného názvu domény v HTTPS|Šifrované SNI se v handshake HTTPS nepodporují.|Dnes pouze Firefox podporuje ESNI prostřednictvím vlastní konfigurace. Doporučeným řešením je tuto funkci vypnout.|
|Klientské certifikáty (TLS)|Klientské certifikáty slouží k vytvoření vzájemné důvěry identity mezi klientem a serverem. Klientské certifikáty se používají během vyjednávání TLS. Azure firewall znovu vyjedná připojení k serveru a nemá přístup k privátnímu klíči klientských certifikátů.|Žádné|
|QUIC/HTTP3|QUIC je nová hlavní verze HTTP. Jedná se o protokol založený na UDP přes 80 (plán) a 443 (SSL). Plně kvalifikovaný název domény/adresa URL/Kontrola TLS se nepodporuje.|Nakonfigurujte předávání protokolu UDP 80/443 jako síťových pravidel.|
|Zabezpečené centrum a vynucené tunelování není v Premium podporované.|V současné době se SKU brány firewall Premium v nasazeních zabezpečeného centra a konfiguracích vynuceného tunelového propojení nepodporují.|Oprava je naplánovaná na GA.|
Nedůvěryhodní certifikáty podepsané zákazníky|Certifikáty podepsané zákazníky nejsou po přijetí z intranetového webového serveru důvěryhodné bránou firewall.|Oprava je naplánovaná na GA.
|Chybné zdrojové a cílové IP adresy v upozorněních pro zprostředkovatelů identity s kontrolou TLS.|Pokud povolíte kontrolu TLS a zprostředkovatelů identity vydáte novou výstrahu, zobrazí se chyba zobrazovaná zdrojová nebo cílová IP adresa (místo původní IP adresy se zobrazí interní IP adresa).|Oprava je naplánovaná na GA.|
|Chybná zdrojová IP adresa v upozorněních s zprostředkovatelů identity pro HTTP (bez kontroly TLS).|Když se provoz HTTP s prostým textem používá a zprostředkovatelů identity vydá novou výstrahu a cíl je veřejný IP adresou, zobrazovaná zdrojová IP adresa je chybná (místo původní IP adresy se zobrazí interní IP adresa).|Oprava je naplánovaná na GA.|
|Šíření certifikátů|Po použití certifikátu certifikační autority v bráně firewall může trvat od 5-10 minut, než se certifikát projeví.|Oprava je naplánovaná na GA.|
|ZPROSTŘEDKOVATELŮ identity obejít|ZPROSTŘEDKOVATELŮ identity nefunguje pro přenos ukončený protokolem TLS a zdrojová IP adresa a zdrojové skupiny IP adres nejsou podporované.|Oprava je naplánovaná na GA.|
|Podpora TLS 1,3|Protokol TLS 1,3 je částečně podporován. Tunelové propojení TLS od klienta k bráně firewall vychází z TLS 1,2 a z brány firewall na externí webový server vychází z TLS 1,3.|Probíhá zkoumání aktualizací.|
|Privátní koncový bod trezoru klíčů|Trezor klíčů podporuje přístup privátního koncového bodu, který omezuje jeho expozici sítě. Pokud je výjimka nakonfigurovaná tak, jak je popsáno v [dokumentaci k trezoru](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)klíčů, můžou důvěryhodné služby Azure obejít toto omezení. Azure Firewall není aktuálně uveden jako důvěryhodná služba a nemá přístup k Key Vault.|Oprava je naplánovaná na GA.|


## <a name="next-steps"></a>Další kroky

- [Další informace o certifikátech Azure Firewall Premium](premium-certificates.md)
- [Nasazení a konfigurace Azure Firewall Premium Preview](premium-deploy.md)
- [Migrace na Azure Firewall Premium Preview](premium-migrate.md)
