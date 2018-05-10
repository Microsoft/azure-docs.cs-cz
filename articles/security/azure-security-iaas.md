---
title: Zabezpečení osvědčených postupů pro IaaS úlohy v Azure | Microsoft Docs
description: " Tím migrace úloh do Azure IaaS přináší možnosti přehodnocovat naše návrhy "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 2498c8b1acff79d50f34c703af77c555a00fd1a2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Osvědčené postupy zabezpečení pro úlohy IaaS v Azure

Protože jste spustili přemýšlení o přesun zátěží Azure infrastruktury jako služby (IaaS), pravděpodobně realizován, že některé aspekty obeznámeni. Prostředí zabezpečení virtuální prostředí již můžete mít. Pokud přesunete do Azure IaaS, můžete použít svoje znalosti v zabezpečení virtuální prostředí a používat novou sadu možností pomáhají zabezpečit vaše prostředky.

Začněme tím informacemi o tom, že by neměl Očekáváme, že aby místních prostředků jako 1: 1 do Azure. Nové možnosti a další výzvy přepněte možnost přehodnocovat existující deigns, nástroje a procesy.

Vaše odpovědnosti pro zabezpečení je založený na typu cloudové služby. Následující graf shrnuje rovnováhu mezi počtem odpovědnost za společnosti Microsoft a můžete:


![Oblasti odpovědnosti](./media/azure-security-iaas/sec-cloudstack-new.png)


Probereme některé možnosti dostupné v Azure, které vám pomohou splnit požadavky na zabezpečení vaší organizace. Mějte na paměti, že požadavky na zabezpečení se může lišit pro různé typy úloh. Mezi tyto doporučené postupy můžete samostatně zabezpečit vaše systémy. Jako nic jiného v zabezpečení budete muset vyberte požadované možnosti a v tématu Jak řešení můžete vzájemně doplňují vyplněním mezery.

## <a name="use-privileged-access-workstations"></a>Použít pracovní stanice privilegovaný přístup.

Organizace často spadají zneužívají k cyberattacks protože správci provádět akce při používání účtů se zvýšenými oprávněními. Obvykle není to neoprávněnému ale protože existující konfiguraci a procesy povolit. Většina těchto uživatelů pochopit riziko z hlediska koncepční tyto akce, ale stále má možnost udělat, je.

Provádění akcí, jako je kontrola e-mailu a prohlížení Internetu pravděpodobně dostatečně nevinnosti. Ale se mohou být vystaveny zvýšenými účty ohrozit podle nebezpečného actors. Procházení aktivity, speciálního e-mailů nebo jinými technikami, můžete použít k získání přístupu na váš podnik. Důrazně doporučujeme použít pracovní stanice pro správu zabezpečení (pily) pro provádění všech úloh správy Azure. Pily představují způsob snížení vystavení náhodných ohrožení zabezpečení.

Pracovní stanice privilegovaného přístupu (PAWs) zadejte pro citlivé úkoly – jeden, který je chráněný před útoky Internet a z nečekaných směrů vyhrazené operačního systému. Oddělení tyto úkoly citlivé a účty ze zařízení a pracovních stanic denně použití poskytuje silnou ochranu. Toto rozdělení omezuje dopad útoky phishing, aplikace a ohrožení zabezpečení operačního systému, různé zosobnění útokům a útokům krádeže přihlašovacích údajů. (protokolováním stisknutí kláves, Pass-the-Hash a Pass-the-Ticket)

Přístup TLAPA je rozšířením zavedené a doporučené postupem použít jednotlivě přiřazené účtu správce. Účet správce, který je oddělený od standardní uživatelský účet. TLAPA poskytuje trustworthy pracovní stanice pro tyto citlivé účty.

Další informace a implementaci pokyny najdete v tématu [pracovní stanice privilegovaného přístupu](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations).

## <a name="use-multi-factor-authentication"></a>Použití vícefaktorového ověřování

V minulosti byl vaší hraniční sítě použít k řízení přístupu k firemním datům. První cloudu, mobile první světě, identita je řídicí: můžete použít k řízení přístupu ke službám IaaS z libovolného zařízení. Můžete ji použít i k získání viditelnost a přehled o tom, jak a kde se používá vaše data. Ochrana digitální identitu Azure uživatele je základním kamenem ochranu vašich předplatných z krádežím identity a dalších cybercrimes.

Jeden z nejvíce výhodné kroků, které můžete provést k zabezpečení účtu je povolit dvoufaktorové ověřování. Dvoufaktorové ověřování představuje způsob ověřování pomocí něco kromě heslo. Pomáhá zmírnit riziko přístup někdo, kdo spravuje získat heslo jiného uživatele.

[Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) pomáhá zabezpečit přístup k datům a aplikacím při splnění požadavků uživatelů pro jednoduchý proces přihlášení. Zajišťuje silné ověřování s celou řadu možností snadno ověření – telefonní hovor, textová zpráva nebo oznámení mobilní aplikace. Uživatelé zvolit metodu, která dávají přednost.

Nejjednodušší způsob, jak používat ověřování Multi-Factor Authentication je Microsoft Authenticator mobilní aplikaci, která lze použít v mobilních zařízení se systémem Windows, iOS a Android. Pomocí nejnovější verze Windows 10 a integraci místní služby Active Directory s Azure Active Directory (Azure AD) [Windows Hello pro firmy](../active-directory/active-directory-azureadjoin-passport-deployment.md) lze použít pro bezproblémové jednotného přihlašování k prostředkům Azure. V takovém případě zařízení s Windows 10 se používá jako druhý faktor ověřování.

Pro účty, které spravují předplatné Azure a účty, které se můžete přihlásit k virtuálním počítačům pomocí služby Multi-Factor Authentication vám dává mnohem vyšší úroveň zabezpečení než použití pouze heslo. Jiné formy dvoufaktorové ověřování může fungovat stejně dobře, ale jejich nasazení může být složité, pokud nejsou již v produkčním prostředí.

Následující snímek obrazovky ukazuje některé z možností dostupných pro Azure Multi-Factor Authentication:

![Možnosti vícefaktorového ověřování](./media/azure-security-iaas/mfa-options.png)

## <a name="limit-and-constrain-administrative-access"></a>Omezení a omezit přístup pro správu

Zabezpečení účtů, které můžete spravovat vaše předplatné Azure je velmi důležité. Ohrozit všechny tyto účty Neguje hodnota všechny ostatní kroky, které může provést k zajištění důvěrnosti a integritu dat. Jako nedávno ilustrované pomocí [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden) interní útoky ohrožují obrovské celkové zabezpečení každé organizace.

Vyhodnoťte jednotlivce pro práva pro správu podle následujících kritérií vypadat přibližně takto:

- Jsou jejich provádění úloh, které vyžadují oprávnění správce?
- Jak často jsou prováděny úlohy?
- Je k dispozici konkrétní důvod, proč nelze provést úlohy jiným správcem jejich jménem?

Všechny ostatní známé Alternativní přístupy k udělení oprávnění a proč každý není přijatelné dokumentů.

Použití správy za běhu zabraňuje zbytečným existenci účty se zvýšenými oprávněními během období, kdy nejsou potřebné tato práva. Účty mít zvýšená práva a po omezenou dobu, tak, aby správci mohou dělat svou práci. Pak tato práva jsou odebrány na konci shift nebo po dokončení úlohy.

Můžete použít [Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md) ke správě, monitorování a řízení přístupu ve vaší organizaci. Pomáhá informován o akce, které jednotlivce provést ve vaší organizaci. Přináší také správy za běhu do služby Azure AD díky zavedení koncept oprávněné správce. Toto jsou jednotlivce, kteří mají účty, které by mohly být udělena práva správce. Tyto typy uživatelů, můžete přejít prostřednictvím procesu aktivace a udělení oprávnění správce po omezenou dobu.


## <a name="use-devtest-labs"></a>Používá DevTest Labs

Pomocí Azure pro testovací prostředí a vývojových prostředí umožňuje organizacím získat lepší schopnost vývoj a testování pomocí rychle zpoždění, které představuje nákup hardwaru. Bohužel chybějících znalost Azure nebo chce, aby urychlit přijetí může vést správce mohl být zbytečně projektovou s přiřazení práv. Toto riziko může neúmyslně vystavit organizaci pro interní útoky. Někteří uživatelé mohou udělit přístup mnohem víc, než by měly mít.

[Azure DevTest Labs](../devtest-lab/devtest-lab-overview.md) služby používá [řízení přístupu](../role-based-access-control/overview.md) (RBAC). Pomocí RBAC, můžete v rámci týmu oddělit povinností do rolí, která udělují pouze úroveň přístupu, které jsou nutné, aby uživatelé pro svou práci. RBAC se dodává s předdefinované role (vlastník, uživatel lab a Přispěvatel). Tyto role můžete použít i k přiřazení práv pro externí partnery a výrazně zjednodušit spolupráci.

Protože DevTest Labs používá RBAC, je možné vytvořit další, [vlastní role](../lab-services/devtest-lab-grant-user-permissions-to-specific-lab-policies.md). DevTest Labs nejen zjednodušuje správu oprávnění, se zjednodušuje proces získávání prostředí zřízený. Pomáhá také řešit potíže s další typické výzvy týmy, které pracují na vývojových a testovacích prostředí. Vyžaduje určitou přípravu, ale z dlouhodobého hlediska, ji budou usnadnily pro váš tým.

Azure DevTest Labs funkce patří:

- Správní kontrolu nad možnosti dostupné uživatelům. Správce můžete centrálně spravovat třeba povolené velikosti virtuálních počítačů, maximální počet virtuálních počítačů a virtuálních počítačů jsou spuštění a vypnutí.
- Automatizace vytváření testovacím prostředí.
- Sledování nákladů.
- Zjednodušená distribuci virtuálních počítačů pro dočasné spolupráce práce.
- Samoobslužné služby, která umožňuje uživatelům umožní zřizovat jejich prostředí pomocí šablon.
- Správa a omezování spotřeby.

![Použití k vytvoření testovacího prostředí DevTest Labs](./media/azure-security-iaas/devtestlabs.png)

Použití DevTest Labs přidružen bez dalších nákladů. Vytvoření labs, zásady, šablony a artefaktů je zdarma. Platíte jenom prostředků Azure používá v prostředích, jako jsou virtuální počítače, účty úložiště a virtuální sítě.



## <a name="control-and-limit-endpoint-access"></a>Řízení a omezení přístupu koncový bod

Hostování labs nebo produkční systémy v Azure znamená, že vaše systémy musí být přístupné z Internetu. Ve výchozím nastavení nový virtuální počítač Windows má portu RDP, který je přístupný z Internetu a virtuální počítač s Linuxem má otevřít port SSH. Provedení kroků ke koncovým bodům limit zveřejněné je potřeba minimalizovat riziko neoprávněného přístupu.

Technologie v Azure můžete omezit přístup do těchto koncových bodů správy. V Azure, můžete použít [skupin zabezpečení sítě](../virtual-network/virtual-networks-nsg.md) (Nsg). Použijete-li pro nasazení Azure Resource Manager, skupiny Nsg omezit přístup ze všech sítí k právě Správa koncových bodů (RDP nebo SSH). Pokud se domníváte, že skupiny Nsg, vezměte v úvahu seznamy ACL směrovače. Můžete je používat k ovládání úzce síťovou komunikaci mezi různé segmenty vaší sítě Azure. Toto je podobná vytváření sítí v hraniční sítě nebo jiné izolované sítě. Navrhují není provoz, ale mohou pomoci s segmentace sítě.


V Azure, můžete nakonfigurovat [site-to-site VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) z vaší místní sítě. Síť site-to-site VPN rozšiřuje vaše místní sítě do cloudu. To vám dává jinou možnost s používáním skupin Nsg, protože můžete také upravit skupiny Nsg nepovolíte přístup odkudkoli jiné než místní sítě. Potom může vyžadovat, aby Správa se provádí první připojením k síti Azure prostřednictvím VPN.

Možnost site-to-site VPN může být nejvíce atraktivní v případech, kde jsou hostování produkční systémy, které jsou úzce integrovaná s vaší místní prostředky v Azure.

Alternativně můžete použít [point-to-site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md) možnost v situacích, ve které chcete spravovat systémy, které nepotřebují přístup k místním prostředkům. Tyto systémy můžete izolovat ve své vlastní virtuální síti Azure. Správci můžou VPN do Azure hostované prostředí z jejich pracovních stanic.

>[!NOTE]
>Změna konfigurace seznamů ACL skupiny Nsg nepovolíte přístup koncových bodů pro správu z Internetu, můžete použít buď možnost VPN.

Další možností vhodné vzhledem k tomu je [Brána vzdálené plochy](../active-directory/authentication/howto-mfaserver-nps-rdg.md) nasazení. Toto nasazení můžete použít k bezpečnému připojování k servery vzdálené plochy přes protokol HTTPS, při použití podrobnější ovládací prvky pro tato připojení.

Funkce, které by mít přístup k patří:

- Správce možnosti omezení počtu připojení na požadavky z konkrétní systémy.
- Ověřování čipovou kartou nebo Azure Multi-Factor Authentication.
- Řízení, přes které systémy někdo můžete připojit přes bránu.
- Možnost ovládat přesměrování zařízení a disku.

## <a name="use-a-key-management-solution"></a>Použít řešení správy klíčů

Pro ochranu dat v cloudu je zásadní zabezpečená správa klíčů. S [Azure Key Vault](../key-vault/key-vault-whatis.md), můžete bezpečně uložit šifrovací klíče a tajné klíče malé jako hesel v modulech hardwarového zabezpečení (HSM). Pro zvýšené bezpečí můžete klíče importovat nebo generovat v modulech HSM.

Procesy Microsoft vaše klíče v FIPS 140-2 Level 2 ověřené HSM (hardware a firmware). Sledování a audit použití klíče s Azure protokolování: přesměrováním protokoly do systému Azure nebo vaše informace o zabezpečení a událostí správy (SIEM) pro další analýzu a zjišťování hrozeb.

Každý, kdo má předplatné Azure můžete vytvořit a použít trezorů klíčů. I když Key Vault přínosný vývojářů a správců zabezpečení, můžete implementovat a spravovat správce, který je zodpovědný za správu služby Azure v organizaci.


## <a name="encrypt-virtual-disks-and-disk-storage"></a>Šifrování virtuálních disků a diskových úložišť

[Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) řeší hrozby odcizení nebo prozrazení před neoprávněným přístupem, které se dosáhne přesunutím disk dat. Na disku lze připojit k jinému systému pro jako způsob obcházení ostatní ovládací prvky zabezpečení. Disk používá šifrování [BitLocker](https://technet.microsoft.com/library/hh831713) v systému Windows a DM-Crypt v systému Linux, k šifrování operačního systému a datové jednotky. Azure Disk Encryption se integruje s Key Vault řídit a spravovat šifrovací klíče. Je k dispozici pro standardní virtuálních počítačů a virtuálních počítačů služby storage úrovně premium.

Další informace najdete v tématu [Azure Disk Encryption v systému Windows a virtuálních počítačů IaaS Linux](azure-security-disk-encryption.md).

[Azure šifrování služby úložiště](../storage/common/storage-service-encryption.md) pomáhá chránit vaše data v klidovém stavu. Je povolena na úrovni účtu úložiště. Se šifruje data, jako je zapsání v našich datacentrech a automaticky je dešifruje jako k němu přístup. Podporuje následující scénáře:

- Šifrování objektů BLOB bloku, doplňovací objekty BLOB, objekty BLOB stránky
- Šifrování archivované šablony uvést do režimu Azure z místního a virtuálních pevných disků
- Šifrování discích operačního systému a dat pro virtuální počítače IaaS, kterou jste vytvořili pomocí virtuální pevné disky

Než přikročíte k šifrování úložiště Azure, nezapomeňte dvě omezení:

- Není k dispozici na klasické účty úložiště.
- Se šifruje jenom data, zapsaná po je povolené šifrování.

## <a name="use-a-centralized-security-management-system"></a>Použít systém správy centralizované zabezpečení

Vaše servery potřeba monitorovat opravy, konfiguraci, události a aktivity, které může považovat za z důvodů zabezpečení. Chcete-li vyřešit tyto otázky, můžete použít [Security Center](https://azure.microsoft.com/services/security-center/) a [Operations Management Suite zabezpečení a dodržování předpisů](https://azure.microsoft.com/services/security-center/). Obě tyto možnosti překročila konfigurace operačního systému. Obsahují taky monitorování konfiguraci základní infrastruktury, jako je konfigurace sítě a použití virtuálního zařízení.

## <a name="manage-operating-systems"></a>Správa operačních systémů

V nasazení IaaS jste stále odpovědní za správu systémů, které nasadíte, stejně jako libovolný server nebo pracovní stanice ve vašem prostředí. Opravy, posílení zabezpečení, přiřazení práv a dalších aktivit, související s údržbou systému jsou stále vaší povinností. Pro systémy, které jsou pevně integrovány s místní prostředky můžete chtít použít stejnou nástroje a postupy, které používáte místní pro věcmi, jako jsou antivirové programy, proti malwaru, opravy a zálohování.

### <a name="harden-systems"></a>Posílení zabezpečení systémů
Všechny virtuální počítače v Azure IaaS by měl být zesílené zabezpečení, tak, aby se vystavit pouze koncové body služby, které jsou požadovány pro aplikace, které jsou nainstalovány. Pro virtuální počítače s Windows, postupujte podle doporučení, které společnost Microsoft publikuje jako směrných plánů pro [Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx) řešení.

Security Compliance Manager je bezplatný nástroj. Můžete ji rychle nakonfigurovat a spravovat stolní počítače, tradičního datacentra a privátních a veřejných cloudů pomocí zásad skupiny a System Center Configuration Manager.

Security Compliance Manager poskytuje připravené nasadit zásady a Desired Configuration Management Pack konfigurace, které jsou testovány. Tyto standardní hodnoty jsou založeny na [doprovodné materiály zabezpečení Microsoft](https://technet.microsoft.com/library/cc184906.aspx) doporučení a odvětví osvědčené postupy. Pomáhají spravovat odlišily konfigurace, požadavky na dodržování předpisů adresu, a snížit bezpečnostní hrozby.

Security Compliance Manager můžete použít k importu aktuální konfiguraci počítače pomocí dvě různé metody. První můžete importovat zásady skupiny služby Active Directory. Druhý, můžete importovat konfiguraci "zlatá master" referenčního počítače pomocí [LocalGPO nástroj](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) zálohování místní zásady skupiny. Místní zásady skupiny můžete poté importovat do správce dodržování předpisů zabezpečení.

Porovnat vaše standardy pro osvědčené postupy v odvětví, přizpůsobit a vytvořit nové zásady a balíčky konfigurace Desired Configuration Management. Standardní hodnoty byly publikovány pro všechny podporované operační systémy, včetně Windows 10 Anniversary Update a Windows Server 2016.


### <a name="install-and-manage-antimalware"></a>Instalace a správa antimalwarových

Pro prostředí, které jsou hostované odděleně od provozního prostředí můžete použít rozšíření antimalwarových k ochraně vašich virtuálních počítačů a cloudových služeb. Se integruje [Azure Security Center](../security-center/security-center-intro.md).


[Microsoft Antimalware](azure-security-antimalware.md) zahrnuje funkcí, jako je ochrana v reálném čase, naplánovanou kontrolu, malwarové nápravy, podpisu aktualizací, aktualizací stroje, ukázky vytváření sestav, shromažďování událostí vyloučení, a [podpora prostředí PowerShell](https://msdn.microsoft.com/library/dn771715.aspx).

![Azure proti malwaru](./media/azure-security-iaas/azantimalware.png)

### <a name="install-the-latest-security-updates"></a>Nainstalujte nejnovější aktualizace zabezpečení
Některé z první úlohy, které zákazníci přesunout do Azure jsou labs a externího systémy. Pokud virtuální počítače Azure hostované hostitelem aplikace nebo služby, které je nutné mít přístup k Internetu, se mimořádnou pozornost opravy. Oprava rámec operačního systému. Neopravenými chybami v aplikacích třetích stran může také způsobit problémy, které se můžete vyhnout, pokud je Správa dobrý oprava na místě.

### <a name="deploy-and-test-a-backup-solution"></a>Nasaďte a otestujte řešení zálohování

Stejně jako aktualizace zabezpečení zálohování musí zpracovávat stejným způsobem, že zpracovat jiná operace. To platí pro systémy, které jsou součástí vašeho produkčního prostředí rozšíření do cloudu. Test a vývojářů systémy musí následovat strategii zálohování, které poskytují možnosti obnovení, které jsou podobné kterými se uživatelé zvykli, o svých zkušenostech s místními prostředími.

Úlohy v produkčním prostředí přesunuta do Azure by měl integrovat existující řešení zálohování, pokud je to možné. Nebo můžete použít [Azure Backup](../backup/backup-azure-arm-vms.md) k vám budou snadněji řešit požadavky zálohy.


## <a name="monitor"></a>Monitorování

[Security Center](../security-center/security-center-intro.md) poskytuje probíhající hodnocení stavu zabezpečení prostředků Azure k identifikovala potenciální ohrožení zabezpečení. Seznam doporučení vás provede procesem konfigurace potřebných kontrol.

Příklady obsahují:

- Zřizování antimalwaru, aby pomohl identifikovat a odebrat škodlivý software.
- Konfigurace skupin zabezpečení sítě a pravidla pro řízení přenosu do virtuálních počítačů.
- Zřizování brány firewall aplikace webové zvýšení ochrany proti útokům zaměřeným webových aplikací.
- Nasazení chybějících aktualizací systému.
- Adresování konfigurací operačního systému, které neodpovídají doporučeným standardním hodnotám.

Následující obrázek ukazuje některé z možností, které se dají povolit ve službě Security Center.

![Zásad Azure Security Center](./media/azure-security-iaas/security-center-policies.png)

[Služby Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) je Microsoft cloudové IT správu řešení, které pomáhá spravovat a chránit místní a cloudové infrastruktury. Protože Operations Management Suite je implementovaný jako cloudová služba, dá se nasadit rychle a s minimálními investic do prostředků infrastruktury.

Nové funkce jsou automaticky doručit vyhnout se tak průběžnou údržbu a upgradovat náklady. Služby Operations Management Suite je integrován s nástrojem System Center Operations Manager. Obsahuje různé součásti, které vám pomohou lépe spravovat Azure úlohy, včetně [zabezpečení a dodržování předpisů](../operations-management-suite/oms-security-getting-started.md) modulu.

Funkce zabezpečení a dodržování předpisů v Operations Management Suite slouží k zobrazení informací o vašich prostředků. Tyto informace jsou rozděleny do čtyř hlavních kategorií:

- **Zabezpečení domény**: dále prozkoumat záznamy zabezpečení v čase. Přístup k posouzením malwaru, informace o aktualizacích hodnocení, informace o zabezpečení sítě, přístupu a identit a počítače s událostmi zabezpečení. Využít výhod rychlý přístup k řídicímu panelu Azure Security Center.
- **Významné problémy**: rychle zjistit počet aktivní problémy a závažnost těchto problémů.
- **Detekce (preview)**: identifikovat útok vzory vizualizací výstrahy zabezpečení nimž dochází před vašich prostředků.
- **Hrozby intelligence**: identifikovat útok vzory vizualizací celkový počet servery s odchozím škodlivým provozem IP, typu zjištění ohrožení a mapu, která ukazuje, kde jsou tyto IP adresy pocházejících z.
- **Běžné dotazy zabezpečení**: Zobrazit seznam nejčastějších dotazů zabezpečení, které můžete použít k monitorování prostředí. Po kliknutí na tlačítko jednu z těchto dotazů **vyhledávání** okno otevře a zobrazí výsledky pro tento dotaz.

Následující snímek obrazovky ukazuje příklad informace, které můžete zobrazit Operations Management Suite.

![Směrné plány zabezpečení Operations Management Suite](./media/azure-security-iaas/oms-security-baseline.png)

## <a name="next-steps"></a>Další postup

* [Blog týmu zabezpečení Azure](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Středisko Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)
* [Osvědčené postupy zabezpečení Azure a vzory](security-best-practices-and-patterns.md)
