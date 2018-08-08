---
title: Osvědčené postupy zabezpečení pro IaaS úloh v Azure | Dokumentace Microsoftu
description: " Migrace úloh do Azure IaaS přináší příležitosti pro opětovné vyhodnocení našich návrhů "
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
ms.date: 06/14/2018
ms.author: barclayn
ms.openlocfilehash: 7c28459aa04c67db8abda54d9f14eb417bd8ed60
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618593"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Osvědčené postupy zabezpečení pro úlohy IaaS v Azure

Ve většině infrastruktura jako služba (IaaS) scénáře [Azure virtuální počítače (VM)](https://docs.microsoft.com/azure/virtual-machines/) jsou hlavní úlohy pro organizace, které používají cloud computing. Tento fakt je obzvláště zřejmé v [hybridních scénářů](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) kde chcete pomalu migrace úloh do cloudu organizace. V takových scénářích, postupujte [obecné otázky zabezpečení pro IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)a použijte osvědčené postupy zabezpečení pro všechny virtuální počítače.

Vaše odpovědnosti pro zabezpečení podle typu cloudovou službu. Následující diagram obsahuje souhrn zůstatek odpovědnost za Microsoftu a můžete:

![Oblasti zodpovědnosti](./media/azure-security-iaas/sec-cloudstack-new.png)

Požadavky na zabezpečení se liší v závislosti na řadě faktorů včetně různé druhy úloh. Není jednou z těchto osvědčených postupů můžete samostatně zabezpečit vaše systémy. Jako nic jiného v zabezpečení budete muset vybrat odpovídající možnosti a naleznete v tématu Jak řešení můžete vzájemně doplňují vyplněním mezery.

Tento článek popisuje různé osvědčené postupy zabezpečení virtuálních počítačů, každý odvozený od zákazníků a vlastní prostředí s přímým přístupem pomocí virtuálních počítačů.

Osvědčené postupy jsou založené na konsenzus názorů a práci s aktuální možnosti platformy Azure a sady funkcí. Protože můžou časem změnit názory a technologií, tento článek bude aktualizován odrážet provedené změny.

## <a name="use-privileged-access-workstations"></a>Použití pracovních stanic s privilegovaným přístupem

Organizace často spadají zneužívají vůči kyberútokům protože správci provádět akce při používání účtů se zvýšenými oprávněními. To nemusí být výsledek škodlivých aktivit, dochází, protože existující konfiguraci a procesy povolit. Většina tito uživatelé vědomi rizika z těchto akcí z hlediska koncepční, ale i nadále je provést.

Provádění akcí, jako je kontrola e-mailu a procházení Internetu zdá se, že dostatečně nevinnosti. Ale může být zveřejňovaly se zvýšenými oprávněními účty ohrozit podle útočníky. Procházení aktivity, speciálně e-mailů nebo jiné techniky je možné získat přístup k vaší organizace. Důrazně doporučujeme použít pracovní stanice pro zabezpečenou správu (Microsoftu) pro provádění všechny úkoly správy Azure. Microsoftu představují způsob, jak tím omezit vystavení náhodného ohrožení zabezpečení.

Pracovní stanice s privilegovaným přístupem (Paw) poskytují vyhrazený operační systém pro citlivé úlohy – ten, který je chráněný před útoky z Internetu a jinými vektory hrozeb. Oddělení citlivých úloh a účty ze zařízení a denní použití pracovních stanic nabízí silnou ochranu. Toto oddělení omezuje dopady útoky typu phishing, aplikace a ohrožení zabezpečení operačního systému, různými útoky se zneužitím zosobnění a útoků využívajících krádež přihlašovacích údajů. (protokolováním stisknutí kláves, Pass-the-Hash a Pass-the-Ticket)

Přístup s privilegovaným PŘÍSTUPEM je rozšířením zavedeného a doporučený postup použít jednotlivě přiřazený účet pro správu. Účet správce je oddělené od standardního uživatelského účtu. Důvěryhodné pracovní stanice privilegovaným PŘÍSTUPEM poskytuje pro citlivé účty.

Další informace a implementaci pokyny najdete v tématu [pracovních stanic s privilegovaným přístupem](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations).

## <a name="use-multi-factor-authentication"></a>Použití služby Multi-Factor Authentication

V minulosti použil hraniční sítě pro řízení přístupu k firemním datům. Ve světě upřednostňujícím a mobilní zařízení, je identita rovina řízení: použít k řízení přístupu ke službám IaaS z libovolného zařízení. Můžete také použít ho k získání viditelnost a přehled o tom, kde a jak se vaše data využívají. Ochrana digitální identity Azure uživatelů je základním kamenem ochranu vašich předplatných před krádeží identit a další cybercrimes.

Jednou z nejpřínosnější kroky, které můžete provést k zabezpečení účtu je chcete povolit dvoufaktorové ověřování. Dvoufaktorové ověřování je způsob, jak ověřování pomocí něco kromě hesla. Pomáhá zmírnit riziko přístupu někdo, kdo spravuje zobrazíte heslo někoho jiného.

[Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) pomáhá chránit přístup k datům a aplikacím a současně plní požadavky uživatelů na jednoduchý přihlašovací proces. Nabízí silné ověřování s řadou jednoduchých možností – telefonní hovor, textová zpráva nebo oznámení přes mobilní aplikaci. Uživatelé zvolit metodu, která co jim vyhovuje.

Nejjednodušší způsob, jak používat ověřování Multi-Factor Authentication je mobilní aplikaci Microsoft Authenticator, který lze použít na mobilních zařízeních se systémem Windows, iOS a Android. V nejnovější verzi Windows 10 a integrace v místním Active Directory s Azure Active Directory (Azure AD) [Windows Hello pro firmy](../active-directory/active-directory-azureadjoin-passport-deployment.md) lze použít pro bezproblémové jednotné přihlašování k prostředkům Azure. V takovém případě zařízení s Windows 10 se používá jako druhý faktor ověřování.

Pro účty, které spravují vaše předplatné Azure a účty, které se můžete přihlásit k virtuálním počítačům pomocí služby Multi-Factor Authentication vám dává mnohem vyšší úroveň zabezpečení než při použití jenom heslo. Další formy dvojúrovňového ověřování může fungovat stejně dobře, ale jejich nasazení může být složité, pokud nejsou již v produkčním prostředí.

Následující snímek obrazovky ukazuje některé možnosti dostupné pro Azure Multi-Factor Authentication:

![Možnosti ověřování službou Multi-Factor Authentication](./media/azure-security-iaas/mfa-options.png)

## <a name="limit-and-constrain-administrative-access"></a>Omezení a omezit přístup pro správu

Zabezpečení účtů, které můžete spravovat své předplatné Azure je důležité. Ohrožení zabezpečení některého z těchto účtů Neguje hodnota všechny další kroky, které můžete použít k zajištění důvěrnosti a integritu dat. Nově ilustrované podle [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden) interní útoky ohrožují obrovské celkové zabezpečení organizace.

Vyhodnoťte jednotlivce pro práva pro správu podle následujících kritérií podobné těmto:

- Jejich provádění úkolů, které vyžadují oprávnění správce?
- Jak často se tyto úkoly provádějí
- Je k dispozici konkrétní důvod, proč úkoly nemůže provádět jiný správce jejich jménem?

Zdokumentujte všech ostatních známých alternativních přístupů k udělení oprávnění a proč se nedají použít.

Správa just-in-time zabrání zbytečné existence účty se zvýšenými oprávněními během období, kdy nejsou potřeba tato práva. Účty musí mít zvýšená práva po omezenou dobu, tak, aby správci mohou vykonávat svoji práci. Potom tato práva se odeberou na konci přechodu nebo při dokončení úkolu.

Můžete použít [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) ke správě, monitorování a řízení přístupu ve vaší organizaci. Pomůže vám na vědomí akce jednotlivce ve vaší organizaci. Přináší také just-in-time správy do služby Azure AD zavedením koncept oprávnění správci. Toto jsou uživatelé, kteří mají účty má potenciál být udělena práva správce. Tyto typy uživatelů můžete projít proces aktivace a udělit oprávnění správce po omezenou dobu.

## <a name="use-devtest-labs"></a>Použití DevTest Labs

Azure pro testovací prostředí a vývojová prostředí eliminuje zpoždění, které zavádí nákup hardwaru. To umožňuje organizacím získat lepší schopnost vývoj a testování. Chybějící seznámení se s Azure nebo chce pomáhají urychlit přijetí na druhé straně může vést správce mohl být zbytečně MIT s přiřazení práv. Toto riziko může neúmyslně zveřejnit organizace interní útokům. Někteří uživatelé můžou udělit přístup mnohem víc, než by měly mít.

[Azure DevTest Labs](../devtest-lab/devtest-lab-overview.md) služba používá [řízení přístupu](../role-based-access-control/overview.md) (RBAC). Pomocí RBAC je můžete oddělit úlohy v rámci týmu do role, které udělit jenom úroveň přístupu uživatelům ke své práci. RBAC se dodává s předdefinovaných rolí (vlastník, uživatele testovacího prostředí a Přispěvatel). Tyto role můžete použít i výrazně zjednodušit spolupráci a přiřadit oprávnění externími partnery.

Vzhledem k tomu DevTest Labs používá RBAC, je možné vytvořit další, [vlastní role](../lab-services/devtest-lab-grant-user-permissions-to-specific-lab-policies.md). DevTest Labs nejen zjednodušuje správu oprávnění, zjednodušuje proces načítání prostředí zřízený. Pomáhá také řešit jiné typické obtíže spojené s týmy, které pracují na vývojová a testovací prostředí. Vyžaduje některé přípravy, ale z dlouhodobého hlediska, ji budou usnadnili pro váš tým.

Azure DevTest Labs k funkcím patří:

- Správní kontrolu nad možnosti dostupné pro uživatele. Správce můžete centrálně spravovat věci, jako je povolené velikosti virtuálního počítače, maximální počet virtuálních počítačů a virtuální počítače jsou spouštěny a provádějte vypnutí.
- Automatizace vytváření prostředí laboratoře.
- Sledování nákladů.
- Zjednodušené distribuci virtuálních počítačů pro dočasné pracovní spolupráci.
- Samoobslužná služba, která umožňuje uživatelům poskytovat jejich labs pomocí šablony.
- Správa a omezování spotřeby.

![DevTest Labs](./media/azure-security-iaas/devtestlabs.png)

Bez dalších poplatků souvisí s využitím DevTest Labs. Vytvoření testovacích prostředí, zásady, šablon a artefaktů je zdarma. Platíte jenom prostředky Azure používané v laboratořích, jako jsou virtuální počítače, účty úložiště a virtuální sítě.

## <a name="control-and-limit-endpoint-access"></a>Řízení a omezení přístupu koncového bodu

Hostování labs nebo produkční systémy v Azure znamená, že vaše systémy musí být přístupné z Internetu. Ve výchozím nastavení vytvoření nového virtuálního počítače Windows má port RDP, který je přístupný z Internetu a má virtuální počítač s Linuxem otevřete port SSH. Kroků ke koncovým bodům limit vystavený je nezbytné minimalizovat riziko neoprávněného přístupu.

Technologie v Azure vám může pomoct omezit přístup k těmto koncovým bodům správy. V Azure, můžete použít [skupiny zabezpečení sítě](../virtual-network/security-overview.md) (Nsg). Pokud používáte Azure Resource Manageru pro nasazení, skupiny zabezpečení sítě omezují přístup ze všech sítí k jenom koncových bodů správy (pomocí protokolu RDP nebo SSH). Pokud přemýšlíte skupin zabezpečení sítě, představte si seznamy ACL směrovače. Můžete využít k důsledně řídit síťové komunikace mezi různé segmenty vaší sítě Azure. Toto je podobné jako vytvoření sítě v hraniční sítě nebo jiné izolované sítě. Nezkoumání provozu, ale mohou pomoci s segmentace sítě.

Omezení přístupu k virtuálním počítačům dynamičtější způsob je začít používat Azure Security center [podle potřeby správy čas](../security-center/security-center-just-in-time.md). Security center můžete zamkněte své virtuální počítače Azure a poskytuje přístup v případě potřeby. Proces funguje tak, že umožňuje přístup na uživatele, který vyžaduje po ověření, založené na jejich [řízení přístupu na základě rolí](../role-based-access-control/role-assignments-portal.md) (RBAC), že máte potřebná oprávnění. Azure Security center se pak proveďte potřebné skupiny zabezpečení sítě (Nsg) a povolení příchozí komunikace.

### <a name="site-to-site-vpnvpn-gatewayvpn-gateway-howto-site-to-site-resource-manager-portalmd"></a>[Site-to-site VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Síť site-to-site VPN rozšiřuje vaši místní síť do cloudu. To vám dává další možnost, jak pomocí skupin zabezpečení sítě, protože můžete také upravit skupiny zabezpečení sítě pro přístup z libovolného místa jiné než v místní síti. Pak může vyžadovat, aby Správa se provádí prvním připojením k síti Azure přes síť VPN.

Možnost site-to-site VPN pravděpodobně nejvíce atraktivní v případech, kde jsou hostování produkční systémy, které jsou úzce integrovány s místní prostředků v Azure.

### <a name="point-to-sitevpn-gatewayvpn-gateway-howto-point-to-site-rm-psmd"></a>[Point-to-site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

V situacích, ve které chcete spravovat systémy, které nepotřebují přístup k místním prostředkům. Tyto systémy se dá izolovat v jejich vlastní virtuální sítě Azure. Správci můžou VPN do Azure hostované prostředí svoje pracovní stanici správce.

>[!NOTE]
>Změna konfigurace seznamů ACL skupiny Nsg k není povolit přístup ke koncovým bodům správy z Internetu můžete použít buď možnost VPN.

### <a name="remote-desktop-gatewayactive-directoryauthenticationhowto-mfaserver-nps-rdgmd"></a>[Brána vzdálené plochy](../active-directory/authentication/howto-mfaserver-nps-rdg.md)

Brána vzdálené plochy můžete použít k bezpečnému propojení servery vzdálené plochy přes protokol HTTPS, při použití podrobnější ovládací prvky pro tato připojení.

Funkce, které bude mít přístup k zahrnují:

- Možnosti správce omezit připojení k žádosti z konkrétní systémy.
- Ověřování pomocí čipové karty nebo ověřování Azure Multi-Factor Authentication.
- Ovládací prvek, přes které systémy někdo může připojit k přes bránu.
- Možnost ovládat přesměrování zařízení a disku.

### <a name="vm-availability"></a>Dostupnost virtuálních počítačů

Pokud virtuální počítač běží důležité aplikace, které je potřeba mít vysokou dostupnost, je důrazně doporučujeme, aby se používají více virtuálních počítačů. Pro zajištění lepší dostupnosti vytvořte alespoň dva virtuální počítače v [dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md).

[Nástroj Azure Load Balancer](../load-balancer/load-balancer-overview.md) také vyžaduje, aby virtuální počítače s vyrovnáváním zatížení patřily do stejné skupiny dostupnosti. Pokud tyto virtuální počítače musí být přístupný z Internetu, musíte nakonfigurovat [internetového nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-internet-overview.md).

## <a name="use-a-key-management-solution"></a>Pomocí řešení správy klíčů

Pro ochranu dat v cloudu je zásadní zabezpečená správa klíčů. S [Azure Key Vault](../key-vault/key-vault-whatis.md), můžete bezpečně ukládat šifrovací klíče a menší tajné kódy, jako jsou hesla v modulech hardwarového zabezpečení (HSM). Pro zvýšené bezpečí můžete klíče importovat nebo generovat v modulech HSM.

Společnost Microsoft bude zpracovávat vaše klíče v FIPS 140-2 úrovně 2 ověřených modulů HSM (hardware a firmware). Sledovat a auditovat použití klíčů díky: protokolům systému Azure nebo vašich informací o zabezpečení a správu událostí (SIEM) k další analýze a detekci hrozeb.

Každý, kdo má předplatné Azure můžete vytvořit a použít trezorům klíčů. Přestože je Key Vault přínosný pro vývojáře a správce zabezpečení, je možné implementovat a spravovat správcem, který je zodpovědný za správu služeb Azure v rámci organizace.

## <a name="encrypt-virtual-disks-and-disk-storage"></a>Šifrování virtuálních disků a diskových úložišť

[Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) řeší hrozby odcizení nebo prozrazení před neoprávněným přístupem, které se dosáhne přesunutím disk dat. Disk může být připojen do jiného systému jako způsob, jak obejít ostatní ovládací prvky zabezpečení. Disk používá šifrování [BitLocker](https://technet.microsoft.com/library/hh831713) ve Windows a DM-Crypt v systému Linux k šifrování operačního systému a datové jednotky. Azure Disk Encryption se integruje se službou Key Vault a řídit a spravovat šifrovací klíče. Je dostupná pro standardní virtuální počítače a virtuální počítače s premium storage.

Další informace najdete v tématu [Azure Disk Encryption ve Windows a virtuálních počítačů IaaS s Linuxem](azure-security-disk-encryption.md).

[Azure Storage Service Encryption](../storage/common/storage-service-encryption.md) pomáhá chránit data v klidovém stavu. Je povolené na úrovni účtu úložiště. Je šifruje data, jako je zapsání v našich datacentrech a automaticky se dešifrují nim přistoupíte. Podporuje následující scénáře:

- Šifrování objektů BLOB bloku, doplňovací objekty BLOB a objekty BLOB stránky
- Šifrování archivované šablony přenést do Azure z místního a virtuálních pevných disků
- Šifrování základního operačního systému a datové disky pro virtuální počítače IaaS, kterou jste vytvořili pomocí virtuálních pevných disků

Než budete pokračovat s Azure Storage a šifrování, mějte dvě omezení:

- Není k dispozici v klasických účtů úložiště.
- Zašifruje pouze data zapsaná po povolení šifrování.

## <a name="use-a-centralized-security-management-system"></a>Použít centralizované zabezpečení systému pro správu

K použití dílčích oprav, konfiguraci, události a aktivity, které může považovat za zajištění zabezpečení musí být monitorované servery. Chcete-li vyřešit tyto otázky, můžete použít [Security Center](https://azure.microsoft.com/services/security-center/) a [Operations Management Suite Security and Compliance](https://azure.microsoft.com/services/security-center/). Obě tyto možnosti nad rámec konfiguraci v operačním systému. Obsahují taky monitorování konfiguraci základní infrastruktury, jako je konfigurace sítě a použijte virtuální zařízení.

## <a name="manage-operating-systems"></a>Správa operačních systémů

V nasazení IaaS zodpovídáte stále pro správu systémů, které nasadíte, stejně jako jiné servery nebo pracovní stanice ve vašem prostředí. Použití dílčích oprav, posílení zabezpečení, přiřazení práv a jinou aktivitu související s údržbou systému jsou stále vaší povinností. Pro systémy, které jsou úzce integrovaná s místní prostředky můžete chtít použít stejné nástroje a postupy, které používáte v místním pro takové věci, jako je antivirová ochrana v programu, antimalwaru, opravy a zálohování.

### <a name="harden-systems"></a>Posílení zabezpečení systémů

Všechny virtuální počítače v Azure IaaS by měl být Posílená tak, aby zveřejňovaly pouze koncové body služby, které jsou požadovány pro aplikace, které jsou nainstalovány. Pro virtuální počítače s Windows, postupujte podle doporučení, které společnost Microsoft publikuje jako směrných plánů pro [Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx) řešení.

Správce dodržování předpisů zabezpečení je bezplatný nástroj. Můžete ho snadno konfigurovat a spravovat stolní počítače, tradičního datacentra a soukromými a veřejnými cloudovými pomocí zásad skupiny a System Center Configuration Manager.

Security Compliance Manager poskytuje připravených k nasazení zásad a balíčky konfigurace Desired Configuration Management, které jsou testovány. Tyto standardní hodnoty jsou založeny na [pokyny Microsoftu ohledně zabezpečení](https://technet.microsoft.com/library/cc184906.aspx) průmyslu a doporučení pro osvědčené postupy. Mohou pomoci při správě odchylky od konfigurace, požadavky na dodržování předpisů adresu a snížit rizika zabezpečení.

Security Compliance Manager můžete použít k importu aktuální konfiguraci počítačů použít dvěma způsoby. Nejprve můžete importovat zásady skupiny služby Active Directory. Za druhé, můžete importovat konfiguraci základního"zlatý" referenčního počítače pomocí [LocalGPO nástroj](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) zálohovat místní zásady skupiny. Pak můžete importovat místní zásady skupiny do správce dodržování předpisů zabezpečení.

Porovnejte vaše standardy pro doporučené postupy, si je přizpůsobit a vytvořit nové zásady a balíčky konfigurace Desired Configuration Management. Standardní hodnoty pro všechny podporované operační systémy, včetně Windows 10 Anniversary Update a Windows serveru 2016 byly publikovány.


### <a name="install-and-manage-antimalware"></a>Nainstalujte a spravujte antimalwarové

Pro prostředí, které jsou hostované odděleně od produkčního prostředí můžete použít antimalwarové rozšíření k ochraně virtuálních počítačů a cloudových služeb. Se integruje se službou [Azure Security Center](../security-center/security-center-intro.md).

[Microsoft Antimalware](azure-security-antimalware.md) zahrnuje funkce, jako je ochrana v reálném čase, naplánovanou kontrolu, malwarové nápravy, aktualizace signatur, aktualizace vyhledávacího stroje, vytváření sestav, shromažďování událostí vyloučení, ukázky a [PodporaprostředíPowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension).

![Azure antimalwaru](./media/azure-security-iaas/azantimalware.png)

### <a name="install-the-latest-security-updates"></a>Nainstalujte nejnovější aktualizace zabezpečení 

Některé z prvních úloh, které zákazníci přejít do Azure jsou cvičení a externích systémů. Pokud vaše virtuální počítače hostované v Azure ukládat aplikace nebo služby, které musí být přístupný na Internetu, se mimořádnou o opravy. Oprava rámec operačního systému. Spojená s neopravenými chybami v aplikacích třetích stran může také vést k problémům, které můžete se vyhnout, pokud je Správa dobré opravu na místě.

### <a name="deploy-and-test-a-backup-solution"></a>Nasazení a testování řešení zálohování

Stejně jako aktualizace zabezpečení musí být zpracována stejným způsobem, že zpracování jiné operace zálohování. To platí pro systémy, které jsou součástí vašeho produkčního prostředí rozšíření do cloudu. Testování a vývoj systémů, musí dodržovat strategii zálohování, které poskytují možnosti obnovení, které jsou podobné uživatelé zvykli, na základě svých zkušeností s místními prostředími.

Produkční úlohy přesunout do Azure se musí integrovat s existující řešení zálohování, pokud je to možné. Nebo můžete použít [Azure Backup](../backup/backup-azure-arm-vms.md) pomáhají řešit požadavky zálohy.

## <a name="monitor"></a>Monitorování

### <a name="security-centersecurity-centersecurity-center-intromd"></a>[Security Center](../security-center/security-center-intro.md)

Služba Security center poskytuje průběžné vyhodnocování stavu zabezpečení vašich prostředků Azure, identifikovat potenciální ohrožení zabezpečení. Seznam doporučení vás provede procesem konfigurace potřebných kontrol.

Příklady obsahují:

- Zřizování antimalwaru, aby vám pomůže identifikovat a odebrat škodlivý software.
- Konfigurace skupin zabezpečení sítě a pravidla pro řízení přenosu do virtuálních počítačů.
- Zřizování firewallů webových aplikací, které pomáhají bránit proti útokům, které cílí vaše webové aplikace.
- Nasazení chybějících aktualizací systému.
- Adresování konfigurací operačního systému, které neodpovídají doporučeným standardním hodnotám.

Následující obrázek ukazuje některé možnosti, které můžete povolit ve službě Security Center.

![Zásady služby Azure Security Center](./media/azure-security-iaas/security-center-policies.png)

### <a name="operations-management-suiteoperations-management-suiteoperations-management-suite-overviewmd"></a>[Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) 

Operations Management Suite je Microsoft cloudové řešení pro správu IT, která vám pomůže spravovat a chránit svoje místní a cloudovou infrastrukturu. Protože Operations Management Suite je implementovaná jako cloudová služba, je možné nasadit, rychle a s minimálními investicemi do prostředků infrastruktury.

Nové funkce jsou doručovány pravidelně, ušetříte z průběžnou údržbu a upgrade náklady. Operations Management Suite se také integruje s nástrojem System Center Operations Manager. Obsahuje různé součásti, které vám pomohou lépe spravovat vaše úlohy Azure, včetně [zabezpečení a dodržování předpisů](../operations-management-suite/oms-security-getting-started.md) modulu.

Funkce zabezpečení a dodržování předpisů v Operations Management Suite můžete použít k zobrazení informací o prostředcích. Tyto informace jsou rozděleny do čtyř hlavních kategorií:

- **Domény zabezpečení**: podrobněji prozkoumat záznamy zabezpečení v průběhu času. Posouzení malwaru, informace o aktualizacích posouzení, informace o zabezpečení sítě, identit a přístupu a počítačů s událostmi zabezpečení. Využijte výhod rychlý přístup k řídicímu panelu Azure Security Center.
- **Významné problémy**: rychle identifikovat celou řadu aktivních problémů a závažnost těchto problémů.
- **Zjištění (preview)**: identifikovat útok vzory vizualizací výstrah zabezpečení, jakmile se objeví na vaše prostředky.
- **Hrozeb**: identifikovat útok vzory vizualizací celkový počet serverů s odchozími škodlivými přenosy, typu škodlivých hrozeb a mapu, která ukazuje, odkud pocházejí tyto IP adresy.
- **Běžné dotazy na zabezpečení**: seznam nejběžnějších bezpečnostních dotazů, které vám umožní monitorovat vaše prostředí. Po kliknutí na některý z těchto dotazů **hledání** okně se otevře a zobrazí výsledky pro daný dotaz.

Následující snímek obrazovky ukazuje příklad informace, které můžete zobrazit Operations Management Suite.

![Základní nastavení zabezpečení pro Operations Management Suite](./media/azure-security-iaas/oms-security-baseline.png)

### <a name="monitor-vm-performance"></a>Monitorování výkonu virtuálních počítačů

Zneužití prostředku může být problém, když procesy virtuální počítač využívat víc prostředků, než by měly. Problémy s výkonem v případě virtuálních počítačů může vést k přerušení služby, který porušuje Princip zabezpečení dostupnosti. Z tohoto důvodu je nutné sledovat přístup k virtuálnímu počítači není pouze reaktivně během dochází k problému, ale také aktivně proti standardních hodnot výkonu, protože při běžném provozu.

Díky analýze [diagnostické protokoly Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/), můžete monitorovat prostředky virtuálních počítačů a identifikovat případné problémy, které by mohlo ohrozit výkon a dostupnost. Rozšíření Azure Diagnostics obsahuje možnosti monitorování a diagnostiku virtuálních počítačů s Windows. Můžete povolit tyto možnosti včetně rozšíření jako součást [šablony Azure Resource Manageru](../virtual-machines/windows/extensions-diagnostics-template.md).

Můžete také použít [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) získat přehled o stavu vašich prostředků.

Organizace, které nechcete monitorovat výkon virtuálního počítače se nepovedlo se určit, jestli jsou určité změny ve výkonu vzorů normálního nebo neobvyklé. Pokud virtuální počítač je spotřebovávat více prostředků, než je obvyklé, takový anomálií může znamenat možný útok z externího zdroje nebo ohroženými procesu spuštěného ve virtuálním počítači.

## <a name="next-steps"></a>Další postup

* [Blog týmu zabezpečení Azure](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Středisko Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)
* [Azure osvědčené postupy a vzory zabezpečení](security-best-practices-and-patterns.md)
