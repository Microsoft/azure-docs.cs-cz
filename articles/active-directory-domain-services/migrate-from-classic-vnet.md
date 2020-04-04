---
title: Migrace doménových služeb Azure AD z klasické virtuální sítě | Dokumenty společnosti Microsoft
description: Zjistěte, jak migrovat existující instanci spravované domény služby Azure AD Domain Services z modelu klasické virtuální sítě do virtuální sítě založené na Správci prostředků.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 6acf9301367ae2c6947f6935c43f420d3d7cac65
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655023"
---
# <a name="migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>Migrace doménových služeb Azure AD z modelu klasické virtuální sítě do Správce prostředků

Služba Azure Active Directory Domain Services (AD DS) podporuje jednorázový přesun pro zákazníky, kteří aktuálně používají model klasické virtuální sítě, do modelu virtuální sítě Resource Manageru. Spravované domény Azure AD DS, které používají model nasazení Správce prostředků, poskytují další funkce, jako jsou zásady jemně odstupňovaných hesel, protokoly auditu a ochrana proti uzamčení účtu.

Tento článek popisuje výhody a aspekty migrace, pak požadované kroky k úspěšné migraci existující instance Azure AD DS.

> [!NOTE]
> V roce 2017 se služby Azure AD Domain Services staly dostupnými pro hostování v síti Azure Resource Manager. Od té doby jsme byli schopni vytvořit bezpečnější službu pomocí moderních funkcí Azure Resource Manager. Vzhledem k tomu, že nasazení Azure Resource Manager plně nahradit klasická nasazení, Nasazení klasické virtuální sítě Azure AD DS bude vyřazena na 1 března 2023.
>
> Další informace naleznete v [oficiálním oznámení o vyřazení](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="overview-of-the-migration-process"></a>Přehled procesu migrace

Proces migrace trvá existující instance Azure AD DS, která běží v klasické virtuální síti a přesune ji do existující virtuální sítě Resource Manager. Migrace se provádí pomocí prostředí PowerShell a má dvě hlavní fáze provádění – *příprava* a *migrace*.

![Přehled procesu migrace pro Azure AD DS](media/migrate-from-classic-vnet/migration-overview.png)

Ve fázi *přípravy* Azure AD DS trvá zálohu domény získat nejnovější snímek uživatelů, skupin a hesel synchronizované do spravované domény. Synchronizace je pak zakázána a cloudová služba, která je hostitelem spravované domény Azure AD DS se odstraní. Během fáze přípravy spravované domény Azure AD DS nelze ověřit uživatele.

![Fáze přípravy pro migraci Azure AD DS](media/migrate-from-classic-vnet/migration-preparation.png)

Ve fázi *migrace* se základní virtuální disky pro řadiče domény z klasické spravované domény Azure AD DS zkopírují a vytvoří virtuální počítače pomocí modelu nasazení Správce prostředků. Spravovaná doména Azure AD DS se pak znovu vytvoří, což zahrnuje konfiguraci LDAPS a DNS. Synchronizace do Azure AD je restartována a certifikáty LDAP se obnoví. Není nutné znovu připojit všechny počítače do spravované domény Azure AD DS – nadále se připojují ke spravované doméně a běží beze změn.

![Migrace Služby Azure AD DS](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>Výhody migrace

Když přesunete spravovanou doménu Azure AD DS pomocí tohoto procesu migrace, vyhnete se nutnosti znovu připojit počítače ke spravované doméně nebo odstranit instanci Azure AD DS a vytvořit ji od začátku. Virtuální počítače se na konci procesu migrace nadále připojují ke spravované doméně Azure AD DS.

Po migraci azure ad ds poskytuje mnoho funkcí, které jsou k dispozici pouze pro domény pomocí resource manager virtuálnísítě, jako jsou:

* Podpora zásad jemně odstupňovaných hesel.
* Ochrana proti uzamčení účtu služby AD.
* E-mailová oznámení o výstrahách ve spravované doméně Azure AD DS.
* Auditujte protokoly pomocí Azure Monitoru.
* Integrace souborů Azure
* Integrace HD Insights

Spravované domény Azure AD DS, které používají virtuální síť Resource Manager, vám pomohou zůstat aktuální s nejnovějšími novými funkcemi. Podpora pro Azure AD DS pomocí klasické virtuální sítě je třeba zastaralá v budoucnu.

## <a name="example-scenarios-for-migration"></a>Příklady scénářů pro migraci

Některé běžné scénáře pro migraci spravované domény Azure AD DS zahrnují následující příklady.

> [!NOTE]
> Nepřevádíte klasickou virtuální síť, dokud nepotvrdíte úspěšnou migraci. Převod virtuální sítě odebere možnost vrátit zpět nebo obnovit spravovanou doménu Azure AD DS, pokud existují nějaké problémy během fáze migrace a ověřování.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Migrace služby Azure AD DS do existující virtuální sítě Správce prostředků (doporučeno)

Běžný scénář je, kde jste již přesunuli další existující klasické prostředky do modelu nasazení Resource Manager a virtuální sítě. Partnerský vztah se pak používá z virtuální sítě Resource Manager do klasické virtuální sítě, která nadále spouštět Azure AD DS. Tento přístup umožňuje aplikacím a službám Správce prostředků používat funkce ověřování a správy spravované domény Azure AD DS ve virtuální síti Classic. Po migraci se všechny prostředky spustí pomocí modelu nasazení Správce prostředků a virtuální sítě.

![Migrace služby Azure AD DS do existující virtuální sítě Správce prostředků](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

Kroky na vysoké úrovni, které jsou součástí tohoto příkladu scénáře migrace, zahrnují následující části:

1. Odeberte existující brány VPN nebo partnerský vztah virtuální sítě nakonfigurovaný ve virtuální síti Classic.
1. Migrujte spravovanou doménu Azure AD DS pomocí kroků popsaných v tomto článku.
1. Otestujte a potvrďte úspěšnou migraci a odstraňte klasickou virtuální síť.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Migrace více prostředků včetně Azure AD DS

V tomto příkladu scénáře migrujete Azure AD DS a další přidružené prostředky z modelu klasickénasazení do modelu nasazení Resource Manager. Pokud některé prostředky nadále běží ve virtuální síti Classic vedle spravované domény Azure AD DS, všechny mohou těžit z migrace do modelu nasazení Správce prostředků.

![Migrace více prostředků do modelu nasazení Správce prostředků](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

Kroky na vysoké úrovni, které jsou součástí tohoto příkladu scénáře migrace, zahrnují následující části:

1. Odeberte existující brány VPN nebo partnerský vztah virtuální sítě nakonfigurovaný ve virtuální síti Classic.
1. Migrujte spravovanou doménu Azure AD DS pomocí kroků popsaných v tomto článku.
1. Nastavte partnerský vztah virtuální sítě mezi klasickou virtuální sítí a sítí Resource Manager.
1. Otestujte a potvrďte úspěšnou migraci.
1. [Přesuňte další klasické prostředky, jako jsou virtuální virtuální společnosti][migrate-iaas].

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Migrace služby Azure AD DS, ale zachování dalších prostředků ve virtuální síti Classic

V tomto příkladu scénáře máte minimální množství prostojů v jedné relaci. Můžete migrovat jenom Azure AD DS do virtuální sítě Správce prostředků a zachovat existující prostředky na klasickém modelu nasazení a virtuální síti. V následujícím období údržby můžete migrovat další prostředky z modelu klasickénasazení a virtuální sítě podle potřeby.

![Migrace jenom azure ad ds do modelu nasazení Správce prostředků](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

Kroky na vysoké úrovni, které jsou součástí tohoto příkladu scénáře migrace, zahrnují následující části:

1. Odeberte existující brány VPN nebo partnerský vztah virtuální sítě nakonfigurovaný ve virtuální síti Classic.
1. Migrujte spravovanou doménu Azure AD DS pomocí kroků popsaných v tomto článku.
1. Nastavte partnerský vztah virtuální sítě mezi klasickou virtuální sítí a novou virtuální sítí Resource Manageru.
1. Později [migrujte další prostředky][migrate-iaas] z klasické virtuální sítě podle potřeby.

## <a name="before-you-begin"></a>Než začnete

Při přípravě a migraci spravované domény Azure AD DS existují některé důležité informace o dostupnosti služeb ověřování a správy. Spravovaná doména Azure AD DS není po určitou dobu během migrace dostupná. Aplikace a služby, které jsou závislé na azure ad ds zkušenosti prostoje během migrace.

> [!IMPORTANT]
> Přečtěte si celý tento článek o migraci a pokyny před zahájením procesu migrace. Proces migrace ovlivňuje dostupnost řadičů domény Azure AD DS po určitá období. Uživatelé, služby a aplikace se nemohou ověřit proti spravované doméně během procesu migrace.

### <a name="ip-addresses"></a>IP adresy

IP adresy řadiče domény pro změnu spravované domény služby Azure AD DS po migraci. Tato změna zahrnuje veřejnou IP adresu zabezpečeného koncového bodu LDAP. Nové adresy IP jsou uvnitř rozsahu adres pro novou podsíť ve virtuální síti Resource Manager.

V případě vrácení zpět ip adresy se může změnit po vrácení zpět.

Azure AD DS obvykle používá první dvě dostupné IP adresy v rozsahu adres, ale to není zaručeno. V současné době nelze zadat ip adresy, které se mají použít po migraci.

### <a name="downtime"></a>Výpadek

Proces migrace zahrnuje řadiče domény, které jsou po určitou dobu offline. Řadiče domény jsou nepřístupné, zatímco Azure AD DS se migruje do modelu nasazení Resource Manager a virtuální sítě. Prostoje jsou v průměru kolem 1 až 3 hodiny. Toto časové období je od okamžiku, kdy jsou řadiče domény přepnuty do režimu offline, až do okamžiku, kdy se první řadič domény vrátí do režimu online. Tento průměr nezahrnuje čas potřebný k replikaci druhého řadiče domény ani dobu, kterou může trvat migrace dalších prostředků do modelu nasazení Správce prostředků.

### <a name="account-lockout"></a>Uzamčení účtu

Spravované domény Azure AD DS, které běží v klasických virtuálních sítích, nemají zásady uzamčení účtů služby AD na místě. Pokud virtuální počítače jsou vystaveny na internetu, útočníci mohou použít metody rozprašování heslem hrubou silou jejich cestu do účtů. Neexistuje žádná zásada uzamčení účtu, která by tyto pokusy zastavila. Pro spravované domény Azure AD DS, které používají model nasazení Resource Manager a virtuální sítě, zásady uzamčení účtu služby AD chrání před těmito útoky spravovacím heslem.

Ve výchozím nastavení 5 pokusů o chybné heslo za 2 minuty uzamkne účet po dobu 30 minut.

Uzamčený účet nelze použít k přihlášení, což může narušit možnost spravovat spravovanou doménu Azure AD DS nebo aplikace spravované účtem. Po migraci spravované domény Azure AD DS, účty může dojít k tomu, co se cítí jako trvalé uzamčení z důvodu opakovaných neúspěšných pokusů o přihlášení. Dva běžné scénáře po migraci zahrnují následující:

* Účet služby, který používá heslo s prošlou platností.
    * Účet služby se opakovaně pokouší přihlásit pomocí hesla, jehož platnost vypršela a které účet uzamkne. Chcete-li tento problém vyřešit, vyhledejte aplikaci nebo virtuální hod s pověřeními, jejichž platnost vypršela, a aktualizujte heslo.
* Škodlivá entita používá pokusy hrubou silou k přihlášení k účtům.
    * Když jsou virtuální počítače vystaveny internetu, útočníci často zkoušejí běžné kombinace uživatelského jména a hesla při pokusu o podepsání. Tyto opakované neúspěšné pokusy o přihlášení mohou účty uzamknout. Nedoporučuje se používat účty správce s obecnými názvy, jako je *správce* nebo *správce*, například k minimalizaci uzamčení účtů pro správu.
    * Minimalizujte počet virtuálních připojení, které jsou vystaveny internetu. [Azure Bastion][azure-bastion] můžete použít k bezpečnému připojení k virtuálním počítačům pomocí portálu Azure.

Pokud máte podezření, že některé účty mohou být uzamčeny po migraci, konečné kroky migrace popisují, jak povolit auditování nebo změnit nastavení zásad jemných hesel.

### <a name="roll-back-and-restore"></a>Vrátit zpět a obnovit

Pokud migrace není úspěšná, je proces vrátit zpět nebo obnovit spravovanou doménu Azure AD DS. Vrácení zpět je samoobslužná možnost okamžitě vrátit stav spravované domény před pokusem o migraci. Technici podpory Azure můžou taky obnovit spravovanou doménu ze zálohy jako poslední možnost. Další informace naleznete v tématu [vrácení zpět nebo obnovení migrace, která selhala](#roll-back-and-restore-from-migration).

### <a name="restrictions-on-available-virtual-networks"></a>Omezení dostupných virtuálních sítí

Existují určitá omezení na virtuální sítě, které spravované domény Azure AD DS lze migrovat do. Cílová virtuální síť Resource Manager musí splňovat následující požadavky:

* Virtuální síť Resource Manager musí být ve stejném předplatném Azure jako klasická virtuální síť, ve které je aktuálně nasazená Služba Azure AD DS.
* Virtuální síť Správce prostředků musí být ve stejné oblasti jako klasická virtuální síť, ve které je aktuálně nasazená služba Azure AD DS.
* Podsíť virtuální sítě Resource Manager by měla mít alespoň 3-5 dostupných ADRES IP.
* Podsíť virtuální sítě Resource Manager by měla být vyhrazená podsíť pro Azure AD DS a neměla by hostovat žádné jiné úlohy.

Další informace o požadavcích na virtuální síť naleznete v [tématu Aspekty návrhu virtuální sítě a možnosti konfigurace][network-considerations].

## <a name="migration-steps"></a>Kroky migrace

Migrace na model nasazení Resource Manager a virtuální síť je rozdělena do 5 hlavních kroků:

| Krok    | Provádí se prostřednictvím  | Odhadovaný čas  | Výpadek  | Vrátit zpět/obnovit? |
|---------|--------------------|-----------------|-----------|-------------------|
| [Krok 1 – Aktualizace a vyhledání nové virtuální sítě](#update-and-verify-virtual-network-settings) | portál Azure | 15 minut | Nejsou vyžadovány žádné prostoje | Není dostupné. |
| [Krok 2 – Příprava spravované domény Azure AD DS pro migraci](#prepare-the-managed-domain-for-migration) | PowerShell | 15 – 30 minut v průměru | Prostoje Azure AD DS se spustí po dokončení tohoto příkazu. | Vraťte se zpět a obnovte k dispozici. |
| [Krok 3 – Přesunutí spravované domény Azure AD DS do existující virtuální sítě](#migrate-the-managed-domain) | PowerShell | 1 – 3 hodiny v průměru | Po dokončení tohoto příkazu je k dispozici jeden řadič domény, prostoje končí. | Při selhání jsou k dispozici vrácení zpět (samoobslužné) i obnovení. |
| [Krok 4 – Testování a čekání na řadič domény repliky](#test-and-verify-connectivity-after-the-migration)| Portál PowerShell a Azure | 1 hodinu nebo více, v závislosti na počtu testů | Oba řadiče domény jsou k dispozici a měly by fungovat normálně. | Není k dispozici. Po úspěšné migraci prvního virtuálního aplikace neexistuje žádná možnost pro vrácení zpět nebo obnovení. |
| [Krok 5 – Volitelné kroky konfigurace](#optional-post-migration-configuration-steps) | Portál Azure a virtuální počítače | Není dostupné. | Nejsou vyžadovány žádné prostoje | Není dostupné. |

> [!IMPORTANT]
> Chcete-li se vyhnout dalším prostojům, přečtěte si před zahájením procesu migrace celý tento článek o migraci a pokyny. Proces migrace ovlivňuje dostupnost řadičů domény Azure AD DS po určitou dobu. Uživatelé, služby a aplikace se nemohou ověřit proti spravované doméně během procesu migrace.

## <a name="update-and-verify-virtual-network-settings"></a>Aktualizace a ověření nastavení virtuální sítě

Před zahájením procesu migrace proveďte následující počáteční kontroly a aktualizace. K těmto krokům může dojít kdykoli před migrací a nemají vliv na provoz spravované domény Azure AD DS.

1. Aktualizujte místní prostředí Azure PowerShell na nejnovější verzi. K dokončení kroků migrace je třeba alespoň verzi *2.3.2*.

    Informace o tom, jak zkontrolovat a aktualizovat verzi PowerShellu, najdete v [tématu Přehled Azure PowerShellu][azure-powershell].

1. Vytvořte nebo zvolte existující virtuální síť Resource Manageru.

    Ujistěte se, že nastavení sítě neblokují potřebné porty potřebné pro Azure AD DS. Porty musí být otevřené ve virtuální síti Classic i ve virtuální síti Resource Manager. Tato nastavení zahrnují směrovací tabulky (i když se nedoporučuje používat tabulky tras) a skupiny zabezpečení sítě.

    Požadované porty naleznete v [tématu Skupiny zabezpečení sítě a požadované porty][network-ports]. Chcete-li minimalizovat problémy se síťovou komunikací, doporučujeme počkat a použít skupinu zabezpečení sítě nebo směrovací tabulku do virtuální sítě Resource Manageru po úspěšném dokončení migrace.

    Poznamenejte si tuto cílovou skupinu prostředků, cílovou virtuální síť a podsíť virtuální sítě. Tyto názvy prostředků se používají během procesu migrace.

1. Zkontrolujte stav spravované domény Azure AD DS na webu Azure Portal. Pokud máte nějaké výstrahy pro spravovanou doménu, vyřešte je před zahájením procesu migrace.
1. Volitelně pokud plánujete přesunout další prostředky do modelu nasazení Resource Manager a virtuální sítě, zkontrolujte, že tyto prostředky lze migrovat. Další informace naleznete v [tématu Migrace prostředků IaaS podporovaná platformou z Klasické ho do Správce prostředků][migrate-iaas].

    > [!NOTE]
    > Nepřevádíte klasickou virtuální síť na virtuální síť Resource Manager. Pokud tak učiníte, neexistuje žádná možnost vrátit zpět nebo obnovit spravovanou doménu Azure AD DS.

## <a name="prepare-the-managed-domain-for-migration"></a>Příprava spravované domény na migraci

Azure PowerShell se používá k přípravě spravované domény Azure AD DS pro migraci. Mezi tyto kroky patří zálohování, pozastavení synchronizace a odstranění cloudové služby, která hostuje Azure AD DS. Po dokončení tohoto kroku Azure AD DS se přenese do offline po určitou dobu. Pokud se krok přípravy nezdaří, můžete [se vrátit zpět do předchozího stavu](#roll-back).

Chcete-li připravit spravovanou doménu Azure AD DS pro migraci, proveďte následující kroky:

1. Nainstalujte `Migrate-Aaads` skript z [Galerie prostředí PowerShell][powershell-script]. Tento skript migrace prostředí PowerShell je digitálně podepsaný týmem technického zabezpečení Azure AD.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. Vytvořte proměnnou, pro kterou chcete uchovat pověření pomocí skriptu pro migraci pomocí rutiny [Získat pověření.][get-credential]

    Uživatelský účet, který zadáte, potřebuje oprávnění *globálního správce* ve vašem tenantovi Azure AD, aby bylo možné povolit oprávnění Azure AD DS a pak *oprávnění přispěvatele* ve vašem předplatném Azure k vytvoření požadovaných prostředků Azure AD DS.

    Po zobrazení výzvy zadejte příslušný uživatelský účet a heslo:

    ```powershell
    $creds = Get-Credential
    ```

1. Nyní spusťte rutinu `Migrate-Aadds` pomocí parametru *-Prepare.* Zadejte *-ManagedDomainFqdn* pro vlastní spravovanou doménu Azure AD DS, jako je *aaddscontoso.com*:

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>Migrace spravované domény

Když je spravovaná doména Azure AD DS připravená a zálohovaná, dá se doména migrovat. Tento krok znovu vytvoří virtuální počítače řadiče domény služby Azure AD Domain Services pomocí modelu nasazení Resource Manager. Tento krok může trvat 1 až 3 hodiny.

Spusťte rutinu `Migrate-Aadds` pomocí parametru *-Commit.* Zadejte *-ManagedDomainFqdn* pro vlastní spravovanou doménu Azure AD DS připravenou v předchozí části, například *aaddscontoso.com*:

Zadejte cílovou skupinu prostředků obsahující virtuální síť, do které chcete migrovat Službu Azure AD DS, například *myResourceGroup*. Zadejte cílovou virtuální síť, například *myVnet*a podsíť, například *DomainServices*.

Po spuštění tohoto příkazu nelze vrátit zpět:

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

Po ověření skriptu je spravovaná doména připravena k migraci, zadejte *Y* a spusťte proces migrace.

> [!IMPORTANT]
> Během procesu migrace nepřeváďte klasickou virtuální síť na virtuální síť Resource Manager. Pokud virtuální síť převedete, nemůžete pak vrátit zpět nebo obnovit spravovanou doménu Azure AD DS, protože původní virtuální síť už nebude existovat.

Každé dvě minuty během procesu migrace indikátor průběhu hlásí aktuální stav, jak je znázorněno v následujícím příkladu výstupu:

![Ukazatel průběhu migrace Azure AD DS](media/migrate-from-classic-vnet/powershell-migration-status.png)

Proces migrace pokračuje v běhu, i když zavřete skript Prostředí PowerShell. Na webu Azure Portal se stav sestav spravované domény jako *Migrace*.

Po úspěšném dokončení migrace můžete zobrazit IP adresu prvního řadiče domény na webu Azure Portal nebo prostřednictvím Azure PowerShellu. Zobrazí se také odhad času u druhého dostupného řadiče domény.

V této fázi můžete volitelně přesunout další existující prostředky z modelu klasické nasazení a virtuální sítě. Nebo můžete zachovat prostředky na modelu klasické nasazení a peer virtuální sítě k sobě po dokončení migrace Azure AD DS.

## <a name="test-and-verify-connectivity-after-the-migration"></a>Testování a ověření připojení po migraci

Může chvíli trvat, než se druhý řadič domény úspěšně nasadí a bude k dispozici pro použití ve spravované doméně Azure AD DS.

S modelem nasazení Správce prostředků se síťové prostředky pro spravovanou doménu Azure AD DS zobrazují na webu Azure Portal nebo Azure PowerShell. Další informace o tom, co jsou tyto síťové prostředky a co dělat, najdete [v tématu síťové prostředky používané službou Azure AD DS][network-resources].

Pokud je k dispozici alespoň jeden řadič domény, proveďte následující kroky konfigurace pro připojení k síti s virtuálními počítačemi:

* **Aktualizace nastavení serveru DNS** Chcete-li, aby ostatní prostředky ve virtuální síti Resource Manager vyřešily a používaly spravovanou doménu Azure AD DS, aktualizujte nastavení DNS pomocí IP adres nových řadičů domény. Portál Azure může tato nastavení automaticky nakonfigurovat za vás. Další informace o konfiguraci virtuální sítě Správce prostředků najdete v [tématu Aktualizace nastavení DNS pro virtuální síť Azure][update-dns].
* **Restartujte virtuální počítače přilehlé k doméně** – jak se mění IP adresy DNS serveru pro řadiče domény Azure AD DS, restartujte všechny virtuální počítače spojené s doménou, aby pak použili nové nastavení serveru DNS. Pokud aplikace nebo virtuální počítače mají ručně nakonfigurované nastavení DNS, ručně aktualizovat je s novým i IP adresy serveru DNS řadičů domény, které jsou zobrazeny na webu Azure Portal.

Nyní otestujte připojení k virtuální síti a překlad názvů. Na virtuálním počítači, který je připojený k virtuální síti Správce prostředků nebo k němu byl připojen, vyzkoušejte následující testy síťové komunikace:

1. Zkontrolujte, zda můžete příkazem ping ping na adresu IP jednoho z řadičů domény, například`ping 10.1.0.4`
    * IP adresy řadičů domény se zobrazují na stránce **Vlastnosti** spravované domény Azure AD DS na webu Azure Portal.
1. Ověření překladu názvů spravované domény, například`nslookup aaddscontoso.com`
    * Zadejte název DNS pro vlastní doménu spravovanou službou Azure AD DS, abyste ověřili, že nastavení DNS jsou správná a překládá.

Druhý řadič domény by měl být k dispozici 1–2 hodiny po dokončení rutiny migrace. Pokud chcete zkontrolovat, jestli je druhý řadič domény k dispozici, podívejte se na stránku **Vlastnosti** spravované domény Azure AD DS na webu Azure Portal. Pokud jsou zobrazeny dvě adresy IP, je připraven druhý řadič domény.

## <a name="optional-post-migration-configuration-steps"></a>Volitelné kroky konfigurace po migraci

Po úspěšném dokončení procesu migrace zahrnují některé volitelné kroky konfigurace povolení protokolů auditování nebo e-mailových oznámení nebo aktualizaci zásad jemných hesel.

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Přihlášení k odběru protokolů auditování pomocí Azure Monitoru

Azure AD DS zpřístupňuje protokoly auditu, které pomáhají řešit potíže a zobrazit události v řadičích domény. Další informace naleznete v [tématu Povolení a použití protokolů auditování][security-audits].

Šablony můžete použít ke sledování důležitých informací vystavených v protokolech. Šablona sešitu protokolu auditu může například monitorovat možné uzamčení účtů ve spravované doméně Azure AD DS.

### <a name="configure-azure-ad-domain-services-email-notifications"></a>Konfigurace e-mailových oznámení služby Azure AD Domain Services

Chcete-li být upozorněni, když je zjištěn problém ve spravované doméně Azure AD DS, aktualizujte nastavení e-mailových oznámení na webu Azure Portal. Další informace naleznete v [tématu Konfigurace nastavení oznámení][notifications].

### <a name="update-fine-grained-password-policy"></a>Aktualizace zásad jemných hesel

V případě potřeby můžete aktualizovat zásady jemně odstupňovaných hesel tak, aby byly méně omezující než výchozí konfigurace. Protokoly auditu můžete použít k určení, zda méně omezující nastavení dává smysl, a pak nakonfigurovat zásady podle potřeby. Pomocí následujících kroků vysoké úrovně zkontrolujte a aktualizujte nastavení zásad pro účty, které jsou po migraci opakovaně uzamčeny:

1. [Nakonfigurujte zásady hesel][password-policy] pro méně omezení spravované domény Azure AD DS a sledujte události v protokolech auditu.
1. Pokud některé účty služeb používají hesla s prošlou platností, jak je uvedeno v protokolech auditu, aktualizujte tyto účty správným heslem.
1. Pokud je virtuální virtuální ms vystaven ý internetu, zkontrolujte obecné názvy účtů, jako je *správce*, *uživatel*nebo *host* s vysokými pokusy o přihlášení. Pokud je to možné, aktualizujte tyto virtuální chody tak, aby používaly méně obecně pojmenované účty.
1. Pomocí trasování sítě na virtuálním počítači vyhledejte zdroj útoků a zablokujte, aby se tyto ADRESY IP mohly pokoušet o přihlášení.
1. Pokud existují minimální problémy uzamčení, aktualizujte zásady jemně odstupňovaných hesel tak, aby byly podle potřeby co nejvíce omezující.

### <a name="creating-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Azure AD DS potřebuje skupinu zabezpečení sítě k zabezpečení portů potřebných pro spravovanou doménu a blokovat všechny ostatní příchozí provozy. Tato skupina zabezpečení sítě funguje jako další vrstva ochrany pro uzamčení přístupu ke spravované doméně a není automaticky vytvořena. Chcete-li vytvořit skupinu zabezpečení sítě a otevřít požadované porty, přečtěte si následující kroky:

1. Na webu Azure Portal vyberte prostředek Azure AD DS. Na stránce s přehledem se zobrazí tlačítko pro vytvoření skupiny zabezpečení sítě, pokud není žádná přidružená ke službě Azure AD Domain Services.
1. Používáte-li zabezpečený protokol LDAP, přidejte do skupiny zabezpečení sítě pravidlo umožňující příchozí přenosy pro port *TCP* *636*. Další informace naleznete v [tématu Konfigurace zabezpečeného protokolu LDAP][secure-ldap].

## <a name="roll-back-and-restore-from-migration"></a>Vrácení zpět a obnovení z migrace

Až do určitého bodu v procesu migrace, můžete se rozhodnout vrátit zpět nebo obnovit spravované domény Azure AD DS.

### <a name="roll-back"></a>Vrátit zpět

Pokud dojde k chybě při spuštění rutiny prostředí PowerShell připravit na migraci v kroku 2 nebo pro samotnou migraci v kroku 3, spravované domény Azure AD DS můžete vrátit zpět do původní konfigurace. Toto vrácení zpět vyžaduje původní klasické virtuální sítě. Všimněte si, že IP adresy může stále změnit po vrácení zpět.

Spusťte rutinu `Migrate-Aadds` pomocí parametru *-Abort.* Zadejte *-ManagedDomainFqdn* pro vlastní spravovanou doménu Azure AD DS připravenou v předchozí části, jako je *aaddscontoso.com*a název klasické virtuální sítě, například *myClassicVnet*:

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds
```

### <a name="restore"></a>Obnovení

Jako poslední možnost služby Azure AD Domain Services lze obnovit z poslední dostupné zálohy. Záloha je provedena v kroku 1 migrace a ujistěte se, že nejaktuálnější záloha je k dispozici. Tato záloha je uložena po dobu 30 dnů.

Chcete-li obnovit spravovanou doménu Azure AD DS ze zálohy, [otevřete lístek případu podpory pomocí portálu Azure][azure-support]. Zadejte ID adresáře, název domény a důvod obnovení. Proces podpory a obnovení může trvat několik dní.

## <a name="troubleshooting"></a>Řešení potíží

Pokud máte problémy po migraci do modelu nasazení Správce prostředků, přečtěte si některé z následujících běžných oblastí řešení potíží:

* [Poradce při potížích s připojením k doméně][troubleshoot-domain-join]
* [Poradce při potížích se uzamčením účtu][troubleshoot-account-lockout]
* [Poradce při potížích s přihlášením k účtu][troubleshoot-sign-in]
* [Poradce při potížích se zabezpečeným připojením LDAP][tshoot-ldaps]

## <a name="next-steps"></a>Další kroky

Když se spravovaná doména Azure AD DS migruje do modelu nasazení Správce prostředků, [vytvořte virtuální počítač s Windows a připojte se k němu a][join-windows] pak [nainstalujte nástroje pro správu][tutorial-create-management-vm].

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/overview
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/windows/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/
