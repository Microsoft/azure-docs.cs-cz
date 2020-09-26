---
title: Migrace Azure AD Domain Services z klasické virtuální sítě | Microsoft Docs
description: Naučte se migrovat existující Azure AD Domain Services spravovanou doménu z modelu klasických virtuálních sítí do virtuální sítě založené na Správce prostředků.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2020
ms.author: iainfou
ms.openlocfilehash: ef05704ea03316ef0c95510e27ee630ddcfb0b44
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91266900"
---
# <a name="migrate-azure-active-directory-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>Migrace Azure Active Directory Domain Services z modelu klasických virtuálních sítí do Správce prostředků

Azure Active Directory Domain Services (Azure služba AD DS) podporuje jednorázové přesunutí pro zákazníky, kteří aktuálně používají model klasických virtuálních sítí pro model Správce prostředků virtuální sítě. Azure služba AD DS spravované domény, které používají model nasazení Správce prostředků poskytují další funkce, jako jsou jemně odstupňované zásady pro hesla, protokoly auditu a ochrana před uzamčením účtu.

V tomto článku najdete popis důležitých informací o migraci a o tom, jaké kroky je potřeba k úspěšné migraci existující spravované domény. Některé výhody najdete v tématu [výhody migrace z modelu nasazení Classic na správce prostředků v Azure služba AD DS][migration-benefits].

> [!NOTE]
> V 2017 je Azure AD Domain Services k dispozici pro hostování v Azure Resource Manager síti. Od té doby jsme dokázali vytvořit bezpečnější službu pomocí moderních možností Azure Resource Manager. Vzhledem k tomu, že Azure Resource Manager nasazení plně nahrazují klasická nasazení, nasazení Azure služba AD DS Classic Virtual Network se vyřadí 1. března 2023.
>
> Další informace najdete v [oficiálním oznámení o zastarání](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/).

## <a name="overview-of-the-migration-process"></a>Přehled procesu migrace

Proces migrace převezme existující spravovanou doménu, která běží v klasické virtuální síti, a přesune ji do existující Správce prostředků virtuální sítě. Migrace se provádí pomocí prostředí PowerShell a má dvě hlavní fáze provádění: *Příprava* a *migrace*.

![Přehled procesu migrace pro Azure služba AD DS](media/migrate-from-classic-vnet/migration-overview.png)

Ve fázi *přípravy* Azure služba AD DS postará o zálohu domény, aby bylo možné získat nejnovější snímek uživatelů, skupin a hesel synchronizovaných se spravovanými doménami. Synchronizace je pak zakázaná a cloudová služba, která je hostitelem spravované domény, se odstraní. Ve fázi přípravy nemůže spravovaná doména ověřovat uživatele.

![Příprava pro migraci Azure služba AD DS](media/migrate-from-classic-vnet/migration-preparation.png)

Ve fázi *migrace* se zkopírují základní virtuální disky pro řadiče domény z klasické spravované domény, aby se vytvořily virtuální počítače pomocí modelu nasazení Správce prostředků. Spravovaná doména se pak znovu vytvoří, což zahrnuje protokol LDAPs a konfiguraci DNS. Synchronizace do služby Azure AD se restartuje a obnoví se certifikáty LDAP. Není nutné znovu připojit žádné počítače ke spravované doméně – budou se nadále připojovat ke spravované doméně a běžet beze změn.

![Migrace služba AD DS Azure](media/migrate-from-classic-vnet/migration-process.png)

## <a name="example-scenarios-for-migration"></a>Příklady scénářů pro migraci

Mezi běžné scénáře migrace spravované domény patří následující příklady.

> [!NOTE]
> Neprovádějte převod klasické virtuální sítě, dokud nepotvrdíte úspěšnou migraci. Pokud během fází migrace a ověření dojde k nějakým potížím, převod virtuální sítě odebere zpět nebo obnoví spravovanou doménu.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Migrace Azure služba AD DS do existující Správce prostředků virtuální sítě (doporučeno)

Běžným scénářem je, že už jste přesunuli jiné existující klasické prostředky do modelu nasazení Správce prostředků a virtuální sítě. Partnerský vztah se pak použije z Správce prostředků virtuální sítě do klasické virtuální sítě, která dál spouští Azure služba AD DS. Tento přístup umožňuje Správce prostředků aplikacím a službám používat funkce ověřování a správy spravované domény v klasické virtuální síti. Po migraci se všechny prostředky spustí pomocí modelu nasazení Správce prostředků a virtuální sítě.

![Migrace Azure služba AD DS do existující Správce prostředků virtuální sítě](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

Kroky vysoké úrovně, které se týkají tohoto ukázkového scénáře migrace, zahrnují tyto části:

1. Odeberte existující brány VPN nebo partnerský vztah virtuálních sítí konfigurovaný v klasické virtuální síti.
1. Pomocí kroků uvedených v tomto článku migrujte spravovanou doménu.
1. Otestujte a potvrďte úspěšnou migraci a pak odstraňte klasickou virtuální síť.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Migrace několika prostředků včetně Azure služba AD DS

V tomto ukázkovém scénáři migrujete služba AD DS Azure a další přidružené prostředky z modelu nasazení Classic do modelu nasazení Správce prostředků. Pokud některé prostředky pokračují v provozu v klasické virtuální síti společně se spravovanou doménou, můžou všechny využít výhod migrace na model nasazení Správce prostředků.

![Migrace několika prostředků do modelu nasazení Správce prostředků](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

Kroky vysoké úrovně, které se týkají tohoto ukázkového scénáře migrace, zahrnují tyto části:

1. Odeberte existující brány VPN nebo partnerský vztah virtuálních sítí konfigurovaný v klasické virtuální síti.
1. Pomocí kroků uvedených v tomto článku migrujte spravovanou doménu.
1. Nastavte partnerský vztah virtuální sítě mezi klasickými virtuálními sítěmi a Správce prostředků sítě.
1. Otestujte a potvrďte úspěšnou migraci.
1. [Přesuňte další klasické prostředky, jako jsou virtuální počítače][migrate-iaas].

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Migrace služba AD DS Azure, ale udržování dalších prostředků v klasické virtuální síti

V tomto ukázkovém scénáři máte minimální dobu výpadku v jedné relaci. Migrujete služba AD DS Azure pouze do Správce prostředků virtuální sítě a zachováte stávající prostředky v modelu nasazení Classic a ve virtuální síti. V následující době údržby můžete podle potřeby migrovat další prostředky z modelu nasazení Classic a virtuální sítě.

![Migrovat jenom Azure služba AD DS do modelu nasazení Správce prostředků](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

Kroky vysoké úrovně, které se týkají tohoto ukázkového scénáře migrace, zahrnují tyto části:

1. Odeberte existující brány VPN nebo partnerský vztah virtuálních sítí konfigurovaný v klasické virtuální síti.
1. Pomocí kroků uvedených v tomto článku migrujte spravovanou doménu.
1. Nastavte partnerský vztah virtuální sítě mezi klasickou virtuální sítí a novou Správce prostředkůovou virtuální sítí.
1. Později v případě potřeby [migrujte další prostředky][migrate-iaas] z klasické virtuální sítě.

## <a name="before-you-begin"></a>Než začnete

Při přípravě a následné migraci spravované domény jsou k dispozici některé okolnosti týkající se dostupnosti služeb ověřování a správy. Spravovaná doména není během migrace k dispozici v časovém intervalu. Aplikace a služby, které spoléhají na Azure služba AD DS v průběhu migrace nezpůsobují výpadek.

> [!IMPORTANT]
> Před zahájením procesu migrace si přečtěte část tohoto článku a pokyny k migraci. Proces migrace má vliv na dostupnost řadičů domény Azure služba AD DS po časových obdobích. Uživatelé, služby a aplikace se nemůžou během procesu migrace ověřit ve spravované doméně.

### <a name="ip-addresses"></a>IP adresy

Po migraci se IP adresy řadiče domény pro spravovanou doménu změnily. Tato změna zahrnuje veřejnou IP adresu pro koncový bod zabezpečeného LDAP. Nové IP adresy jsou v rozsahu adres pro novou podsíť ve virtuální síti Správce prostředků.

Pokud se potřebujete vrátit zpátky, můžou se IP adresy po vrácení zpátky změnit.

Azure služba AD DS obvykle používá první dvě dostupné IP adresy v rozsahu adres, není to ale zaručené. V tuto chvíli nemůžete zadat IP adresy, které se mají použít po migraci.

### <a name="downtime"></a>Výpadek

Proces migrace zahrnuje řadiče domény, které jsou po určitou dobu offline. Řadiče domény jsou nedostupné, když je Azure služba AD DS migrována do modelu nasazení Správce prostředků a virtuální sítě.

V průměru je výpadek zhruba 1 až 3 hodiny. Toto časové období vychází z doby, kdy jsou řadiče domény přepnuty do stavu offline, kdy se první řadič domény vrátí zpět do režimu online. Tento průměr nezahrnuje čas potřebný k replikaci druhého řadiče domény, nebo čas, který může trvat k migraci dalších prostředků do modelu nasazení Správce prostředků.

### <a name="account-lockout"></a>Uzamčení účtu

Spravované domény, které běží na klasických virtuálních sítích, nemají zásady uzamčení účtů služby AD. Pokud jsou virtuální počítače přístupné pro Internet, útočníci můžou použít metody postřikování hesel k hrubě vynucenému způsobu jejich používání. Neexistují žádné zásady uzamčení účtů k zastavení těchto pokusů. U spravovaných domén, které používají model nasazení Správce prostředků a virtuální sítě, jsou zásady uzamčení účtů služby AD chráněny proti těmto útokům pomocí hesla.

Ve výchozím nastavení se 5 špatných pokusů o zadání hesla během 2 minut zamkne účet na 30 minut.

Uzamčený účet se nedá použít k přihlášení, což může narušit schopnost spravovat spravovanou doménu nebo aplikace, které spravuje daný účet. Po migraci spravované domény můžou mít účty k diskontu jako trvalé uzamčení kvůli opakovaným neúspěšným pokusům o přihlášení. Mezi dva běžné scénáře migrace patří následující:

* Účet služby, který používá heslo s vypršenou platností
    * Účet služby se opakovaně snaží přihlásit pomocí hesla, které vypršelo, které uzamkne účet. Pokud to chcete opravit, vyhledejte aplikaci nebo virtuální počítač s přihlašovacími údaji, které vypršely, a aktualizujte heslo.
* Škodlivá entita pomocí nepřímých pokusů o přihlášení k účtům.
    * Když jsou virtuální počítače přístupné pro Internet, útočníci často při pokusu o podepsání používají společné kombinace uživatelského jména a hesla. Tyto opakované neúspěšné pokusy o přihlášení můžou tyto účty uzamknout. Nedoporučujeme používat účty správců s obecnými názvy, jako je například *správce* nebo *správce*, pro minimalizaci účtů pro správu, které se mají odemknout.
    * Minimalizujte počet virtuálních počítačů, které jsou zpřístupněny pro Internet. [Azure bastionu][azure-bastion] můžete použít k bezpečnému připojení k virtuálním počítačům pomocí Azure Portal.

Pokud se domníváte, že některé účty můžou být po migraci uzamčené, postup konečného postupu migrace popisuje, jak povolit auditování, nebo změnit nastavení zásad podrobného hesla.

### <a name="roll-back-and-restore"></a>Vrácení zpět a obnovení

Pokud migrace není úspěšná, je proces vrácení nebo obnovení spravované domény. Vrácení zpět představuje možnost samoobslužné služby, která okamžitě vrátí stav spravované domény do doby před pokusem o migraci. Technici podpory Azure můžou také obnovit spravovanou doménu ze zálohy jako poslední. Další informace najdete v tématu [Jak vrátit zpět nebo obnovit z neúspěšné migrace](#roll-back-and-restore-from-migration).

### <a name="restrictions-on-available-virtual-networks"></a>Omezení pro dostupné virtuální sítě

Existují určitá omezení pro virtuální sítě, na které může být spravovaná doména migrována. Cílová Správce prostředků virtuální síť musí splňovat následující požadavky:

* Virtuální síť Správce prostředků musí být ve stejném předplatném Azure jako klasická virtuální síť, ve které je služba Azure služba AD DS aktuálně nasazená.
* Správce prostředků virtuální síť musí být ve stejné oblasti jako klasická virtuální síť, ve které je služba Azure služba AD DS aktuálně nasazená.
* Podsíť Správce prostředků virtuální sítě musí mít minimálně 3-5 dostupných IP adres.
* Podsíť Správce prostředků virtuální sítě by měla být vyhrazenou podsítí pro Azure služba AD DS a neměla by hostovat žádné další úlohy.

Další informace o požadavcích na virtuální síť najdete v tématu věnovaném [hlediskům návrhu virtuální sítě a možnostem konfigurace][network-considerations].

Musíte také vytvořit skupinu zabezpečení sítě, která bude omezovat provoz ve virtuální síti pro spravovanou doménu. Nástroj Azure Load Balancer úrovně Standard se vytvoří během procesu migrace, který vyžaduje, aby se tato pravidla mohla umístit. Tato skupina zabezpečení sítě zabezpečuje službu Azure služba AD DS a je potřeba, aby správně fungovala spravovaná doména.

Další informace o tom, jaká pravidla se vyžadují, najdete v článku [skupiny zabezpečení sítě a požadované porty pro Azure služba AD DS](network-considerations.md#network-security-groups-and-required-ports).

### <a name="ldaps-and-tlsssl-certificate-expiration"></a>Vypršení platnosti certifikátu LDAPs a TLS/SSL

Pokud je vaše spravovaná doména nakonfigurovaná pro LDAPs, zkontrolujte, jestli je váš aktuální certifikát TLS/SSL platný po dobu delší než 30 dní. Certifikát, jehož platnost vyprší během následujících 30 dnů, způsobí selhání procesů migrace. V případě potřeby obnovte certifikát a použijte ho ve spravované doméně a pak zahajte proces migrace.

## <a name="migration-steps"></a>Kroky migrace

Migrace na model nasazení Správce prostředků a virtuální síť je rozdělená na 5 hlavních kroků:

| Krok    | Provedeno prostřednictvím  | Odhadovaný čas  | Výpadek  | Vrátit zpět a obnovit? |
|---------|--------------------|-----------------|-----------|-------------------|
| [Krok 1 – aktualizace a vyhledání nové virtuální sítě](#update-and-verify-virtual-network-settings) | portál Azure | 15 minut | Nepožaduje se žádný výpadek | Není k dispozici |
| [Krok 2 – Příprava spravované domény na migraci](#prepare-the-managed-domain-for-migration) | PowerShell | 15 – 30 minut v průměru | Výpadek služby Azure služba AD DS začíná po dokončení tohoto příkazu. | Vrácení zpět a obnovení k dispozici. |
| [Krok 3 – přesunutí spravované domény do existující virtuální sítě](#migrate-the-managed-domain) | PowerShell | 1 – 3 hodiny v průměru | Po dokončení tohoto příkazu je k dispozici jeden řadič domény, výpadek skončí. | Při selhání jsou k dispozici obě vrácení zpět (Samoobslužná služba) i obnovení. |
| [Krok 4 – testování a čekání na repliku řadiče domény](#test-and-verify-connectivity-after-the-migration)| PowerShell a Azure Portal | 1 hodina nebo více, v závislosti na počtu testů | Oba řadiče domény jsou k dispozici a měly by fungovat normálně. | Není k dispozici. Po úspěšné migraci prvního virtuálního počítače není k dispozici možnost vrácení nebo obnovení. |
| [Krok 5 – volitelné kroky konfigurace](#optional-post-migration-configuration-steps) | Azure Portal a virtuální počítače | Není k dispozici | Nepožaduje se žádný výpadek | Není k dispozici |

> [!IMPORTANT]
> Abyste se vyhnuli dalším výpadkům, přečtěte si tento článek a pokyny k migraci před zahájením procesu migrace. Proces migrace má na určitou dobu vliv na dostupnost řadičů domény Azure služba AD DS. Uživatelé, služby a aplikace se nemůžou během procesu migrace ověřit ve spravované doméně.

## <a name="update-and-verify-virtual-network-settings"></a>Aktualizace a ověření nastavení virtuální sítě

Než zahájíte proces migrace, proveďte následující počáteční kontroly a aktualizace. K těmto krokům může dojít kdykoli před migrací a neovlivňují provoz spravované domény.

1. Aktualizujte své místní Azure PowerShell prostředí na nejnovější verzi. K dokončení kroků migrace potřebujete alespoň verzi *2.3.2*.

    Informace o tom, jak ověřit a aktualizovat verzi PowerShellu, najdete v tématu [přehled Azure PowerShell][azure-powershell].

1. Vytvořte nebo vyberte existující Správce prostředků virtuální síť.

    Ujistěte se, že nastavení sítě neblokují potřebné porty vyžadované pro Azure služba AD DS. Porty musí být otevřené v klasické virtuální síti i ve virtuální síti Správce prostředků. Mezi tato nastavení patří směrovací tabulky (i když se nedoporučuje používat směrovací tabulky) a skupiny zabezpečení sítě.

    Azure služba AD DS potřebuje skupinu zabezpečení sítě k zabezpečení portů potřebných pro spravovanou doménu a blokování všech ostatních příchozích přenosů. Tato skupina zabezpečení sítě funguje jako dodatečná vrstva ochrany pro uzamknutí přístupu ke spravované doméně. Chcete-li zobrazit požadované porty, přečtěte si téma [skupiny zabezpečení sítě a požadované porty][network-ports].

    Pokud používáte zabezpečený protokol LDAP, přidejte do skupiny zabezpečení sítě pravidlo, které povolí příchozí provoz pro *TCP* port TCP *636*. Další informace najdete v tématu [uzamčení zabezpečeného přístupu LDAP přes Internet](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet) .

    Poznamenejte si tuto cílovou skupinu prostředků, cílovou virtuální síť a podsíť cílové virtuální sítě. Tyto názvy prostředků se používají během procesu migrace.

1. Ověřte stav spravované domény v Azure Portal. Pokud máte pro spravovanou doménu nějaké výstrahy, vyřešte je před zahájením procesu migrace.
1. Případně, pokud plánujete přesunout další prostředky do modelu nasazení Správce prostředků a virtuální síť, potvrďte, že je možné tyto prostředky migrovat. Další informace najdete v tématu [migrace prostředků IaaS podporovaných platformou z klasický na správce prostředků][migrate-iaas].

    > [!NOTE]
    > Neprovádějte převod klasické virtuální sítě na virtuální síť Správce prostředků. V takovém případě není k dispozici možnost vrátit zpět nebo obnovit spravovanou doménu.

## <a name="prepare-the-managed-domain-for-migration"></a>Příprava spravované domény na migraci

Azure PowerShell slouží k přípravě spravované domény pro migraci. Tyto kroky zahrnují zálohování, pozastavení synchronizace a odstranění cloudové služby, která hostuje Azure služba AD DS. Po dokončení tohoto kroku bude Azure služba AD DS po určitou dobu offline. Pokud krok přípravy neproběhne úspěšně, můžete [se vrátit k předchozímu stavu](#roll-back).

K přípravě spravované domény pro migraci proveďte následující kroky:

1. Nainstalujte `Migrate-Aaads` skript z [Galerie prostředí PowerShell][powershell-script]. Tento skript migrace prostředí PowerShell je digitálně podepsaný technickým týmem Azure AD.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. Pomocí rutiny [Get-Credential][get-credential] Vytvořte proměnnou pro uložení přihlašovacích údajů pro migrační skript.

    Uživatelský účet, který zadáte, potřebuje v tenantovi Azure AD oprávnění *globálního správce* , aby bylo možné v předplatném Azure povolit oprávnění k vytváření požadovaných prostředků azure služba AD DS služba AD DS a potom *přispěvatele* .

    Po zobrazení výzvy zadejte příslušný uživatelský účet a heslo:

    ```powershell
    $creds = Get-Credential
    ```
    
1. Definujte proměnnou pro ID předplatného Azure. V případě potřeby můžete pomocí rutiny [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) zobrazit seznam a zobrazit ID vašich předplatných. V následujícím příkazu zadejte vlastní ID předplatného:

   ```powershell
   $subscriptionId = 'yourSubscriptionId'
   ```

1. Nyní spusťte `Migrate-Aadds` rutinu pomocí parametru *-Prepare* . Zadejte *ManagedDomainFqdn* pro vlastní spravovanou doménu, jako je například *aaddscontoso.com*:

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds `
        -SubscriptionId $subscriptionId
    ```

## <a name="migrate-the-managed-domain"></a>Migrace spravované domény

Když je spravovaná doména připravena a zálohována, je možné doménu migrovat. V tomto kroku se vytvoří virtuální počítače Azure služba AD DS řadiče domény pomocí modelu nasazení Správce prostředků. Dokončení tohoto kroku může trvat 1 až 3 hodiny.

Spusťte `Migrate-Aadds` rutinu s použitím parametru *-Commit* . Zadejte *ManagedDomainFqdn* pro vaši vlastní spravovanou doménu připravenou v předchozí části, jako je například *aaddscontoso.com*:

Zadejte cílovou skupinu prostředků obsahující virtuální síť, do které chcete migrovat Azure služba AD DS, například *myResourceGroup*. Zadejte cílovou virtuální síť, například *myVnet*, a podsíť, jako je například *DomainServices*.

Po spuštění tohoto příkazu se nemůžete vrátit zpátky:

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds `
    -SubscriptionId $subscriptionId
```

Jakmile skript ověří, že je spravovaná doména připravená pro migraci, zadejte *Y* a spusťte proces migrace.

> [!IMPORTANT]
> Během procesu migrace nepřeveďte klasickou virtuální síť do Správce prostředků virtuální sítě. Pokud převedete virtuální síť, nemůžete tuto spravovanou doménu vrátit zpět ani obnovit, protože původní virtuální síť už neexistuje.

Každé dvě minuty během procesu migrace zobrazí indikátor průběhu stav aktuálního stavu, jak je znázorněno v následujícím příkladu výstupu:

![Indikátor průběhu migrace služba AD DS Azure](media/migrate-from-classic-vnet/powershell-migration-status.png)

Proces migrace pokračuje v běhu, i když zavřete PowerShellový skript. V Azure Portal se stav sestavy spravované domény provádí jako *migrace*.

Po úspěšném dokončení migrace můžete zobrazit IP adresu prvního řadiče domény v Azure Portal nebo prostřednictvím Azure PowerShell. Zobrazí se také časový odhad druhého řadiče domény, který je k dispozici.

V této fázi můžete volitelně přesunout další existující prostředky z modelu nasazení Classic a virtuální sítě. Nebo můžete po dokončení migrace do služby Azure služba AD DS zachovat prostředky v modelu nasazení Classic a navzájem partnerským virtuálním sítím.

## <a name="test-and-verify-connectivity-after-the-migration"></a>Testování a ověření připojení po migraci

Může trvat nějakou dobu, než se druhý řadič domény úspěšně nasadí a bude k dispozici pro použití ve spravované doméně.

Pomocí modelu nasazení Správce prostředků se v Azure Portal nebo Azure PowerShell zobrazí síťové prostředky pro spravovanou doménu. Další informace o tom, jaké jsou tyto síťové prostředky, najdete v tématu [síťové prostředky používané službou Azure služba AD DS][network-resources].

Pokud je k dispozici alespoň jeden řadič domény, proveďte následující kroky konfigurace pro síťové připojení k virtuálním počítačům:

* **Aktualizovat nastavení serveru DNS** Pokud chcete umožnit jiným prostředkům ve Správce prostředků virtuální síti přeložit a používat spravovanou doménu, aktualizujte nastavení DNS pomocí IP adres nových řadičů domény. Azure Portal může tato nastavení automaticky nakonfigurovat.

    Další informace o tom, jak nakonfigurovat Správce prostředků virtuální síť, najdete v tématu [aktualizace nastavení DNS pro virtuální síť Azure][update-dns].
* **Restartujte virtuální počítače připojené k doméně** – jako IP adresy serveru DNS pro řadiče domény služba AD DS Azure, restartujte všechny virtuální počítače připojené k doméně, aby pak používali nové nastavení serveru DNS. Pokud aplikace nebo virtuální počítače mají ručně nakonfigurovaná nastavení DNS, aktualizujte je ručně s novými IP adresami serveru DNS řadičů domény, které jsou uvedené v Azure Portal.

Nyní otestujte připojení k virtuální síti a překlad názvů. Na virtuálním počítači, který je připojený k virtuální síti Správce prostředků, nebo na něj partnerský vztah, vyzkoušejte následující testy komunikace v síti:

1. Ověřte, jestli můžete testovat IP adresu jednoho z řadičů domény, třeba `ping 10.1.0.4`
    * IP adresy řadičů domény se zobrazují na stránce **vlastnosti** spravované domény v Azure Portal.
1. Ověřte překlad názvů spravované domény, například `nslookup aaddscontoso.com`
    * Zadejte název DNS vlastní spravované domény, abyste ověřili, že jsou nastavení DNS správná a vyřešená.

Druhý řadič domény by měl být k dispozici 1-2 hodin po dokončení rutiny migrace. Pokud chcete zjistit, jestli je druhý řadič domény dostupný, podívejte se na stránku **vlastnosti** spravované domény v Azure Portal. Pokud se zobrazí dvě IP adresy, druhý řadič domény je připravený.

## <a name="optional-post-migration-configuration-steps"></a>Volitelné kroky konfigurace po migraci

Po úspěšném dokončení procesu migrace zahrnuje některé volitelné kroky konfigurace povolení protokolů auditu nebo e-mailových oznámení nebo aktualizaci jemně odstupňovaných zásad hesel.

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Přihlášení k odběru protokolů auditu pomocí Azure Monitor

Azure služba AD DS zveřejňuje protokoly auditu, které vám pomůžou řešit a zobrazovat události na řadičích domény. Další informace najdete v tématu [povolení a používání protokolů auditu][security-audits].

Pomocí šablon můžete monitorovat důležité informace, které jsou k dispozici v protokolech. Například Šablona sešitu protokolu auditu může sledovat možné uzamčení účtu ve spravované doméně.

### <a name="configure-email-notifications"></a>Konfigurace e-mailových oznámení

Chcete-li být upozorněni na zjištění problému ve spravované doméně, aktualizujte nastavení e-mailových oznámení v Azure Portal. Další informace najdete v tématu [Konfigurace nastavení oznámení][notifications].

### <a name="update-fine-grained-password-policy"></a>Aktualizace jemně odstupňovaných zásad hesel

V případě potřeby můžete jemně odstupňované zásady hesel aktualizovat tak, aby byly méně omezující než výchozí konfigurace. Protokoly auditu můžete použít k určení, jestli má méně omezující nastavení smysl, a pak zásadu podle potřeby nakonfigurovat. Pomocí následujících kroků vysoké úrovně zkontrolujte a aktualizujte nastavení zásad pro účty, které se po migraci opakovaně zablokují:

1. [Nakonfigurujte zásady hesel][password-policy] pro méně omezení ve spravované doméně a sledujte události v protokolech auditu.
1. Pokud některé účty služeb používají hesla s vypršenou platností podle identifikace v protokolech auditu, aktualizujte tyto účty správným heslem.
1. Pokud je virtuální počítač vystavený Internetu, přečtěte si obecné názvy účtů, jako je například *správce*, *uživatel*nebo *Host* s vysokým počtem pokusů o přihlášení. Pokud je to možné, aktualizujte tyto virtuální počítače tak, aby používaly méně obecně pojmenovaných účtů.
1. Pomocí trasování sítě na virtuálním počítači vyhledejte zdroj útoků a zajistěte, aby se tyto IP adresy mohly pokoušet o přihlášení.
1. Pokud jsou problémy s minimálním uzamčením, aktualizujte podrobné zásady pro hesla tak, aby byly podle potřeby co nejvíce omezující.

## <a name="roll-back-and-restore-from-migration"></a>Vrácení zpět a obnovení z migrace

Až do určitého bodu v procesu migrace se můžete rozhodnout vrátit zpět nebo obnovit spravovanou doménu.

### <a name="roll-back"></a>Vrátit zpět

Pokud při spuštění rutiny prostředí PowerShell dojde k chybě pro přípravu migrace v kroku 2 nebo pro samotnou migraci v kroku 3, spravovaná doména se může vrátit k původní konfiguraci. Tato návratová záloha vyžaduje původní klasickou virtuální síť. Tyto IP adresy se můžou i po vrácení změn změnit.

Spusťte `Migrate-Aadds` rutinu s použitím parametru *-Abort* . Zadejte *ManagedDomainFqdn* pro vaši vlastní spravovanou doménu připravenou v předchozí části, jako je třeba *aaddscontoso.com*, a název klasické virtuální sítě, například *myClassicVnet*:

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds `
    -SubscriptionId $subscriptionId
```

### <a name="restore"></a>Obnovení

Jako poslední možnost se Azure AD Domain Services dá obnovit z poslední dostupné zálohy. Zálohování se provádí v kroku 1 migrace, aby se zajistilo, že je k dispozici nejaktuálnější záloha. Tato záloha se ukládá po dobu 30 dnů.

Chcete-li obnovit spravovanou doménu ze zálohy, [otevřete lístek podpory případu pomocí Azure Portal][azure-support]. Zadejte ID adresáře, název domény a důvod pro obnovení. Dokončení procesu podpory a obnovení může trvat několik dní.

## <a name="troubleshooting"></a>Řešení potíží

Pokud máte po migraci do modelu nasazení Správce prostředků problémy, přečtěte si některé z následujících běžných oblastí pro odstraňování potíží:

* [Řešení problémů s připojením k doméně][troubleshoot-domain-join]
* [Řešení problémů s uzamčením účtu][troubleshoot-account-lockout]
* [Řešení potíží s přihlašováním k účtu][troubleshoot-sign-in]
* [Řešení potíží se zabezpečeným připojením LDAP][tshoot-ldaps]

## <a name="next-steps"></a>Další kroky

Po migraci spravované domény do modelu nasazení Správce prostředků [vytvořte a připojte se k doméně virtuální počítač s Windows][join-windows] a pak [nainstalujte nástroje pro správu][tutorial-create-management-vm].

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/
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
[migration-benefits]: concepts-migration-benefits.md

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/
