---
title: Zabezpečení dat a šifrování osvědčené postupy | Microsoft Docs
description: Tento článek obsahuje sadu osvědčené postupy pro zabezpečení dat a šifrování pomocí součástí možnosti Azure.
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: 159bdf681761b9fc46f77cbcf25a210db11d1d9b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Doporučené postupy zabezpečení služby Azure Data a šifrování
Jeden z klíčů do ochrany dat v cloudu je monitorování účtů pro možné stavy, která může nastat vaše data a jaké ovládací prvky jsou k dispozici pro tento stav. Pro účely služby Azure data bude šifrování osvědčené postupy pro zabezpečení a doporučení ohledně stavy následující data:

* Na rest: To zahrnuje všechny informace, které kontejnerů, objektů úložiště a typy, které existují staticky na fyzickém médiu, být ho magnetické nebo optický disk.
* Na cestě: Když data přenášena mezi komponenty, umístění nebo programy, jako třeba přes síť, přes service bus (z lokálního prostředí do cloudu a naopak, včetně hybridních připojení, jako je například ExpressRoute), nebo během vstupu a výstupu, ho je představit jako za provozu.

V tomto článku se budeme zabývat kolekce dat Azure zabezpečení a šifrování osvědčené postupy. Tyto doporučené postupy jsou odvozeny od našich zkušeností s Azure data zabezpečení a šifrování a prostředí zákazníků jako sami.

Pro každý osvědčený postup vysvětlíme:

* Co je osvědčeným postupem
* Proč chcete povolit tento osvědčený postup
* Pokud se nepodaří povolit osvědčený postup, co může být výsledkem
* Možné alternativy doporučené postupy
* Jak můžete informace o povolení osvědčený postup

Tento článek zabezpečení dat v Azure a osvědčené postupy pro šifrování je založen na konsenzu stanovisko a možnostech platformy Azure a sady funkcí, které jsou v době, kdy byla zapsána v tomto článku. Názory a technologie v průběhu času mění a v tomto článku budeme je aktualizovat v pravidelných intervalech, aby odrážela tyto změny.

Azure data zabezpečení a šifrování osvědčené postupy popsané v tomto článku patří:

* Vynutit ověřování Multi-Factor authentication
* Řízení přístupu (RBAC) na základě role pomocí
* Šifrování virtuálních počítačů Azure
* Použití modelů zabezpečení hardwaru
* Spravovat zabezpečení pracovních stanic
* Zapnout šifrování dat SQL
* Ochranu přenášených dat
* Vynutit šifrování dat na úrovni souborů

## <a name="enforce-multi-factor-authentication"></a>Vynutit ověřování Multi-Factor Authentication
Prvním krokem při přístupu k datům a řízení v nástroji Microsoft Azure je k ověření uživatele. [Azure Multi-Factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md) je metoda ověření identity uživatele pomocí jiné metody než jenom uživatelské jméno a heslo. Toto ověřování metoda pomáhá chránit přístup k datům a aplikacím při splnění požadavků uživatelů pro jednoduchý proces přihlášení.

Povolením Azure MFA pro uživatele přidáte druhou vrstvu zabezpečení uživatelská přihlášení a transakce. V takovém případě transakci může získávat přístup k dokumentu umístěná na souborovém serveru nebo ve vaší službě SharePoint Online. Azure MFA také pomáhá IT, které sníží pravděpodobnost, že ohrožené pověření bude mít přístup k dat organizace.

Například: Pokud vynutit Azure MFA pro uživatele a nakonfigurujte ho na použití telefonního hovoru nebo textové zprávy jako ověřování, pokud dojde k ohrožení bezpečnosti přihlašovací údaje uživatele, útočník nebudou mít přístup k jakémukoli prostředku, vzhledem k tomu, že nebudete mít přístup k telefonu uživatele. Organizace, které nepřidávejte tento další vrstvu ochrany identit budou náchylnější pro útoku krádeží přihlašovacích údajů, což může vést k ohrožení zabezpečení dat.

Jeden alternativou pro organizace, které chcete zachovat ověřování řízení místní je použít [Azure Multi-Factor Authentication Server](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), označované taky jako MFA na místě. Pomocí této metody přesto bude možné vynutit ověřování Multi-Factor authentication, a zajistit přitom ochranu MFA server místní.

Další informace o Azure MFA, najdete v článku [Začínáme s Azure Multi-Factor Authentication v cloudu](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="use-role-based-access-control-rbac"></a>Řízení přístupu (RBAC) na základě Role pomocí
Omezení přístupu na základě [potřebovat znát](https://en.wikipedia.org/wiki/Need_to_know) a [nejnižší oprávnění](https://en.wikipedia.org/wiki/Principle_of_least_privilege) Principy zabezpečení. To je nutné pro organizace, které chcete vynutit zásady zabezpečení pro přístup k datům. Azure na základě rolí řízení přístupu (RBAC) slouží k přiřazení oprávnění pro uživatele, skupiny a aplikace v určité oboru. Předplatné, skupinu prostředků nebo jediný zdroj, může být oboru přiřazení role.

Můžete využít [předdefinované role RBAC](../role-based-access-control/built-in-roles.md) v Azure přiřadit oprávnění pro uživatele. Zvažte použití *Přispěvatel účet úložiště* pro operátorům cloudu, kteří potřebují spravovat účty úložiště a *Classic Přispěvatel účet úložiště* rolí ke správě klasické účty úložiště. Operátoři cloudu, které potřebuje ke správě virtuálních počítačů a účet úložiště, zvažte přidá do *Přispěvatel virtuálních počítačů* role.

Organizace, které nebudou vynucovat řízení přístupu dat s využitím funkcí, jako je RBAC může být poskytnutí další oprávnění, než je nezbytné pro své uživatele. To může vést k ohrožení zabezpečení dat tak, že někteří uživatelé, kteří mají přístup k datům, který by neměl mít na prvním místě.

Další informace o Azure RBAC přečíst v článku [řízení přístupu](../role-based-access-control/role-assignments-portal.md).

## <a name="encrypt-azure-virtual-machines"></a>Šifrování virtuálních počítačů Azure
Pro mnoho společností [šifrování dat v klidovém stavu](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) je povinný krok k suverenity data o ochraně osobních údajů a dodržování předpisů a data. Azure Disk Encryption umožňuje správcům IT šifrování disků systému Windows a Linux IaaS virtuálního počítače (VM). Azure Disk Encryption využívá funkci oborový standard BitLocker systému Windows a DM-Crypt funkce systému Linux zajistit šifrování svazku operačního systému a datové disky.

Můžete využít Azure Disk Encryption k ochraně a ochranu dat, aby splňovaly vaše požadavky na dodržování předpisů a zabezpečení organizace. Organizace měli také zvážit použití šifrování pro zmírnění rizika data související s neoprávněný přístup. Dále je doporučeno, šifrování jednotky před zápis do nich citlivá data.

Ujistěte se, že pokud chcete chránit data uložená v účtu úložiště Azure šifrovat objemy dat Virtuálního počítače a spouštěcí svazek. Zabezpečení šifrovacích klíčů a tajných klíčů s využitím [Azure Key Vault](../key-vault/key-vault-whatis.md).

Pro místní servery Windows zvažte následující šifrování osvědčené postupy:

* Použití [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) pro šifrování dat
* Ukládání informací pro obnovení ve službě AD DS.
* Pokud není k dispozici žádné obavy, že ohrožený klíče Bitlockeru, doporučujeme, abyste buď formátu jednotky a odstraňte všechny instance BitLocker metadata z jednotky nebo dešifrovat a znovu zašifrovat celou jednotku.

Organizace, které není vynuceno šifrování dat budou s větší pravděpodobností vystaveno problémy s integritou dat, jako jsou uživatelé škodlivý nebo neautorizovaných serverů krádež dat a dojde k ohrožení účty neoprávněnému přístupu k datům ve formátu zrušte. Kromě těchto rizik společností, které mají povinnost dodržovat předpisy odvětví, musí prokázat, že jsou péče a používáte ovládací prvky správné zabezpečení pro zvýšení zabezpečení dat.

Další informace o Azure Disk Encryption přečíst v článku [Azure Disk Encryption pro systém Windows a virtuálních počítačů IaaS Linux](azure-security-disk-encryption.md).

## <a name="use-hardware-security-modules"></a>Použijte moduly hardwarového zabezpečení
Odvětví šifrování řešení použít k šifrování dat tajných klíčů. Proto je důležité, aby tyto klíče jsou bezpečně uložené. Správa klíčů stane nedílnou součástí ochrany dat, vzhledem k tomu, že bude možné využít k uložení tajných klíčů, které slouží k šifrování dat.

Používá Azure disk encryption [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) můžete řídit a spravovat disku šifrovacích klíčů a tajných klíčů ve vašem předplatném trezoru klíčů, while, zajistíte, že všechna data v disky virtuálního počítače jsou zašifrovaná přinejmenším ve službě Azure storage. Měli byste použít Azure Key Vault auditovat klíče a použití zásad.

Existuje mnoho vyplývajících rizika související s zavedené k ochraně tajných klíčů, které jste použili k šifrování dat nemá potřebné ovládací prvky zabezpečení. Pokud útočník přístup k tajných klíčů, bude mít k dešifrování dat a můžou mít přístup k důvěrným informacím.

Další informace o obecná doporučení pro správy certifikátů ve službě Azure přečíst v článku [správy certifikátů ve službě Azure: doporučené a nedoporučené postupy](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/).

Další informace o Azure Key Vault najdete v tématu [Začínáme s Azure Key Vault](../key-vault/key-vault-get-started.md).

## <a name="manage-with-secure-workstations"></a>Spravovat zabezpečení pracovních stanic
Vzhledem k tomu, že velká většina útoků cíle koncového uživatele, koncový bod se změní na jednu primární bodů útoku. Pokud útočník ohrožuje koncový bod, si můžete využít přihlašovací údaje uživatele k získání přístupu k datům společnosti. Většina útoků koncový bod je moct využívat výhod skutečnost, že koncoví uživatelé jsou správci ve své místní pracovní stanice.

Tato rizika můžete omezit použitím zabezpečené správy pracovní stanice. Doporučujeme vám, že používáte [privilegovaného přístupu pracovní stanice (TLAPA)](https://technet.microsoft.com/library/mt634654.aspx) redukovat prostor pro útok v pracovních stanicích. Tyto pracovní stanice pro správu zabezpečení můžete zmírnit některé z těchto útoků zajistit vašich dat je bezpečnější. Nezapomeňte použít TLAPA zamknout pracovní stanice a posilovat jejich zabezpečení. Toto je důležitým krokem při zaručí, že vysoké zabezpečení pro citlivé účty, úlohy a ochranu dat.

Nedostatečná služby endpoint protection může ohrozit vaše data, zajistěte, aby vynutit zásady zabezpečení na všech zařízeních, které se používají ke zpracování dat, bez ohledu na umístění dat (cloudové nebo místní).

Další informace o privilegovaný přístup k pracovní stanici přečíst v článku [zabezpečení privilegovaného přístupu](https://technet.microsoft.com/library/mt631194.aspx).

## <a name="enable-sql-data-encryption"></a>Zapnout šifrování dat SQL
[Azure SQL Database transparentní šifrování dat](https://msdn.microsoft.com/library/dn948096.aspx) (TDE) pomáhá chránit před ohrožením škodlivých aktivit provedením v reálném čase šifrování a dešifrování databáze, přidružených záloh a soubory protokolu transakcí v klidovém stavu bez nutnosti změny aplikace.  Šifrování TDE zašifruje úložiště celé databáze pomocí symetrický klíč s názvem šifrovací klíč databáze.

I v případě, že šifrované celý úložiště, je velmi důležité šifrovat taky sama vaší databáze. Toto je implementace obrany v hloubka přístupu, ochrany dat. Pokud používáte [Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) a chcete, chrání citlivá data, jako je například platebních karet nebo čísla sociálního pojištění, můžete šifrovat databáze s FIPS 140-2 ověřené 256 256bitového šifrování AES splňuje požadavky na mnoho oborových standardů (například HIPAA, PCI).

Je důležité si uvědomit, že soubory související s [rozšíření fondu vyrovnávací paměti](https://msdn.microsoft.com/library/dn133176.aspx) (BPE) nejsou šifrují, pokud databáze je zašifrovaná pomocí šifrování TDE. Je nutné použít nástroje šifrování na úrovni systému souborů, jako je nástroj BitLocker nebo [systém souborů EFS](https://technet.microsoft.com/library/cc700811.aspx) (EFS) pro BPE související soubory.

Od oprávněný uživatel, jako je zabezpečení správce nebo správce databáze měli přístup k datům, i v případě, že databáze je šifrován TDE, by navíc dodržíte následující doporučení:

* Ověřování na úrovni databáze serveru SQL
* Pomocí role RBAC ověřování Azure AD
* Uživatelé a aplikace by měla používat samostatné účty k ověření. Tímto způsobem můžete omezit oprávnění udělená uživatelům a aplikacím a snížení rizika škodlivých aktivit
* Implementace zabezpečení na úrovni databáze pomocí pevné databázové role (například db_datareader nebo db_datawriter), nebo můžete vytvořit vlastní role pro vaši aplikaci udělit výslovná oprávnění pro vybranou databázi objekty

Organizace, které nejsou pomocí šifrování na úrovni databáze může být více náchylný na útoky, které mohou ohrozit dat umístěných v databázích SQL.

Další informace o šifrování SQL TDE přečíst v článku [transparentní šifrování dat s Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="protect-data-in-transit"></a>Ochranu přenášených dat
Základní součástí strategie ochrany dat by měly být ochrany dat během přenosu. Vzhledem k tomu, že data bude přesunutí a zpět z mnoho míst, obecné doporučení je vždy používat protokoly SSL/TLS pro výměnu dat v různých umístěních. V některých případech můžete chtít izolovat celý komunikační kanál mezi místní a cloudové infrastruktury pomocí virtuální privátní sítě (VPN).

Pro přesun mezi vaší místní infrastruktury a Azure data měli byste zvážit příslušná bezpečnostní opatření, například HTTPS nebo VPN.

Organizace, které je potřeba zabezpečit přístup z více pracovní stanice nachází v místě k Azure, použijte [Azure site-to-site VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md).

Organizace, které je potřeba zabezpečit přístup z jedné pracovní stanice nachází v místě k Azure, použijte [Point-to-Site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

Větších datových sad se dají přesunout přes vyhrazené vysokorychlostní propojení WAN, jako [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Pokud chcete použít ExpressRoute, můžete také šifrování dat na úrovni aplikace pomocí [SSL/TLS](https://support.microsoft.com/kb/257591) nebo jiné protokoly pro zvýšení ochrany.

Pokud jsou interakci s Azure Storage prostřednictvím portálu Azure, všechny transakce dojít přes HTTPS. [Rozhraní API REST úložiště](https://msdn.microsoft.com/library/azure/dd179355.aspx) přes HTTPS můžete použít také pro interakci s [Azure Storage](https://azure.microsoft.com/services/storage/) a [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Organizace, které se nepodařilo ochranu přenášených dat budou náchylnější pro [útokům man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [odposlouchávání](https://technet.microsoft.com/library/gg195641.aspx) a zneužití relace. Prvním krokem při přístupu k důvěrných dat může být tyto útoky.

Další informace o Azure VPN možnost přečíst v článku [plánování a návrhu pro bránu VPN](../vpn-gateway/vpn-gateway-plan-design.md).

## <a name="enforce-file-level-data-encryption"></a>Vynutit šifrování dat na úrovni souborů
Další vrstvu zabezpečení, která může zvýšit úroveň zabezpečení pro vaše data, je šifrování souboru samostatně, bez ohledu na umístění souboru.

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) používá zásady šifrování, identity a autorizace, které pomáhají zabezpečit soubory a e-mailu. Azure RMS funguje napříč více zařízeními – telefony, tablety a počítače pomocí ochrany v rámci vaší organizace i mimo vaši organizaci. Tato možnost je možné, protože Azure RMS přidá úroveň ochrany, která zůstává s daty, i když opustí prostory vaší organizace.

Pokud používáte službu Azure RMS k ochraně souborů, používáte standardní kryptografie s plnou podporu systému [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Pokud využíváte Azure RMS pro ochranu dat, máte záruku, že zůstane ochrana se souborem, i v případě, že se zkopíruje do úložiště, které není pod kontrolou oddělení IT, třeba do cloudového úložiště. Stejné dojde souborů sdílených prostřednictvím e-mailu, je soubor chráněný jako příloha k e-mailovou zprávu s pokyny, jak chráněnou přílohu otevřít.

Při plánování pro Azure RMS přijetí doporučujeme následující:

* Nainstalujte [aplikace sdílení RMS](https://technet.microsoft.com/library/dn339006.aspx). Tato aplikace se integruje s Office aplikace nainstalováním Office doplňku tak, aby uživatelé mohli snadno chránit soubory přímo.
* Nakonfigurujte aplikace a služby pro podporu Azure RMS
* Vytvoření [vlastní šablony](https://technet.microsoft.com/library/dn642472.aspx) , podle vlastních podnikových požadavků. Příklad: šablonu pro horní tajných dat, která má být použita v všechny hlavní tajný klíč související s e-mailů.

Organizace, které jsou slabé na [klasifikace dat](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) a ochranu souborů může být náchylnější k úniku dat. Bez ochrany správný soubor nebude možné získat obchodní údaje, sledovat zneužití a zabránit škodlivým přístupem k souborům organizace.

Další informace o Azure RMS přečíst v článku [Začínáme se službou Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).
