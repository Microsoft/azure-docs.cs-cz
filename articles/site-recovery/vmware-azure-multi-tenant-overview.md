---
title: Obnovení zotavení po havárii virtuálního počítače VMware s azure site recovery
description: Obsahuje přehled podpory Azure Site Recovery pro zotavení po havárii v systému VMWare do Azure v programu víceklientského prostředí (CSP).
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 840049265d3b6e4d2fddd794646bfd5691aab9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083990"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>Přehled podpory více klientů pro zotavení po havárii společnosti VMware do Azure pomocí csp

[Azure Site Recovery](site-recovery-overview.md) podporuje víceklientská prostředí pro předplatná tenanta. Podporuje také více klienta pro předplatná tenanta, které jsou vytvořeny a spravovány prostřednictvím programu Zprostředkovatele cloudových řešení (CSP) společnosti Microsoft.

Tento článek obsahuje přehled implementace a správy víceklientské služby VMware do replikace Azure.

## <a name="multi-tenant-environments"></a>Prostředí s více klienty

Existují tři hlavní víceklientské modely:

* **Poskytovatel sdílených hostingových služeb (HSP):** Partner vlastní fyzickou infrastrukturu a používá sdílené prostředky (vCenter, datová centra, fyzické úložiště a tak dále) k hostování více virtuálních počítačů klientů na stejné infrastruktuře. Partner může poskytovat správu zotavení po havárii jako spravovanou službu nebo klient může vlastní zotavení po havárii jako samoobslužné řešení.

* **Vyhrazený poskytovatel hostingových služeb:** Partner vlastní fyzickou infrastrukturu, ale používá vyhrazené prostředky (více center vCenters, úložiště fyzických dat a tak dále) k hostování virtuálních počítačů každého klienta na samostatné infrastruktuře. Partner může poskytovat správu zotavení po havárii jako spravovanou službu nebo ji klient může vlastnit jako samoobslužné řešení.

* **Zprostředkovatel spravovaných služeb (MSP):** Zákazník vlastní fyzickou infrastrukturu, která hostuje virtuální zařízení, a partner poskytuje povolení a správu zotavení po havárii.

## <a name="shared-hosting-services-provider-hsp"></a>Poskytovatel sdílených hostingových služeb (HSP)

Další dva scénáře jsou podmnožiny scénáře sdíleného hostování a používají stejné zásady. Rozdíly jsou popsány na konci sdílené hohostingové pokyny.

Základním požadavkem ve scénáři s více tenanty je, že klienti musí být izolováni. Jeden klient by neměl být schopen sledovat, co hostoval jiný klient. V prostředí spravovaném partnerem tento požadavek není tak důležitý jako v samoobslužném prostředí, kde může být kritický. Tento článek předpokládá, že je vyžadována izolace klienta.

Architektura je znázorněna v následujícím diagramu.

![Sdílené HSP s jedním vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Sdílený hosting s jedním serverem vCenter**

V diagramu má každý zákazník samostatný server pro správu. Tato konfigurace omezuje přístup klienta k virtuálním počítačům specifickým pro klienta a umožňuje izolaci klienta. Replikace virtuálních počítačů VMware používá konfigurační server ke zjišťování virtuálních počítačů a k instalaci agentů. Stejné zásady platí pro prostředí s více klienty s přidáním omezení zjišťování virtuálních připojení pomocí řízení přístupu vCenter.

Požadavek na izolaci dat znamená, že všechny citlivé informace o infrastruktuře (například přístupová pověření) zůstávají nezveřejněny pro klienty. Z tohoto důvodu doporučujeme, aby všechny součásti serveru pro správu zůstaly pod výhradní kontrolou partnera. Součásti serveru pro správu jsou:

* Konfigurační server
* Procesní server
* Hlavní cílový server

Pod kontrolou partnera je také samostatný horizontální navýšení kapacity.

## <a name="configuration-server-accounts"></a>Účty konfiguračního serveru

Každý konfigurační server ve scénáři s více klienty používá dva účty:

- **vCenter přístupový účet**: Tento účet se používá ke zjišťování virtuálních účtů klienta. Má přístupová oprávnění vCenter k němu přiřazena. Chcete-li se vyhnout nevracení přístupu, doporučujeme partnerům zadat tato pověření sami v konfiguračním nástroji.

- **Účet přístupu k virtuálnímu počítači**: Tento účet se používá k instalaci agenta služby Mobility na virtuální počítače klienta s automatickým nabízením. Obvykle se jedná o účet domény, který může klient poskytnout partnerovi, nebo účet, který může partner spravovat přímo. Pokud klient nechce sdílet podrobnosti s partnerem přímo, může zadat pověření prostřednictvím časově omezeného přístupu ke konfiguračnímu serveru. Nebo s pomocí partnera může agenta služby Mobility nainstalovat ručně.

## <a name="vcenter-account-requirements"></a>požadavky na účet vCenter

Nakonfigurujte konfigurační server pomocí účtu, ke kterému je přiřazena zvláštní role.

- Přiřazení role musí být použito pro účet přístupu vCenter pro každý objekt vCenter a nesmí být rozšířeno na podřízené objekty. Tato konfigurace zajišťuje izolaci klienta, protože šíření přístupu může mít za následek náhodný přístup k jiným objektům.

    ![Možnost Šíření do podřízených objektů](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- Alternativní přístup je přiřadit uživatelský účet a roli v objektu datového centra a šířit je do podřízených objektů. Pak udělit účet **bez přístupu** role pro každý objekt (například virtuální chod, které patří do jiných klientů), které by měly být nepřístupné pro konkrétního klienta. Tato konfigurace je těžkopádná. Poskytuje ovládací prvky náhodného přístupu, protože každý nový podřízený objekt je také automaticky udělen přístup, který je zděděn od nadřazeného objektu. Proto doporučujeme použít první přístup.

### <a name="create-a-vcenter-account"></a>Vytvoření účtu vCenter

1. Vytvořte novou roli klonováním předdefinované role *jen pro čtení* a pak jí přiřazujte vhodný název (například Azure_Site_Recovery, jak je znázorněno v tomto příkladu).
2. Této roli přiřaďte následující oprávnění:

   * **DataStore**: Přidělit místo, Procházet úložiště dat, Operace souborů nižší úrovně, Odebrat soubor, Aktualizovat soubory virtuálního počítače
   * **Síť**: Přiřazení sítě
   * **Zdroj:** Přiřazení virtuálního počítače fondu prostředků, migrace vypnuté ho virtuálního počítače, migrace na virtuálním počítači
   * **Úkoly**: Vytvoření úkolu, Aktualizace úkolu
   * **Virtuální ho virtuálního počítače – konfigurace**: Vše
   * **Virtuální počítač – interakce** > otázka odpovědi, připojení zařízení, konfigurace médií CD, konfigurace disketových médií, vypnutí, zapnutí, instalace nástrojů VMware
   * **Virtuální ms – inventář** > vytvořit z existujícího, vytvořit nový, registrovat, zrušit registraci
   * **Virtuální počítač – zřizování** > povolit stahování virtuálních strojů, povolit nahrávání souborů virtuálních strojů
   * **Virtuální počítače – správa snímků** > odebrat snímky

       ![Dialogové okno Upravit roli](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Přiřaďte úrovně přístupu k účtu vCenter (používanému v konfiguračním serveru klienta) pro různé objekty takto:

>| Objekt | Role | Poznámky |
>| --- | --- | --- |
>| vCenter | Jen pro čtení | Potřebné pouze k povolení přístupu vCenter pro správu různých objektů. Toto oprávnění můžete odebrat, pokud účet nikdy nebude poskytnut a tenantovi nebo použit pro všechny operace správy v centru. |
>| Datové centrum | Azure_Site_Recovery |  |
>| Hostitelský a hostitelský cluster | Azure_Site_Recovery | Znovu zajišťuje, že přístup je na úrovni objektu, takže pouze přístupní hostitelé mají virtuální chody klienta před převzetím služeb při selhání a po navrácení služeb po obnovení. |
>| Cluster úložiště dat a úložiště dat | Azure_Site_Recovery | Stejné jako předchozí. |
>| Network (Síť) | Azure_Site_Recovery |  |
>| Server pro správu | Azure_Site_Recovery | Zahrnuje přístup ke všem součástem (CS, PS a MT) mimo počítač CS. |
>| Virtuální klientské služby | Azure_Site_Recovery | Zajišťuje, že tento přístup získají také nové virtuální počítače klienta konkrétního klienta, jinak nebudou zjistitelné prostřednictvím portálu Azure. |

Přístup k účtu vCenter je nyní dokončen. Tento krok splňuje požadavek na minimální oprávnění k dokončení operací navrácení služeb po selhání. Tato přístupová oprávnění můžete také použít s existujícími zásadami. Stačí upravit stávající oprávnění nastavená tak, aby zahrnovala oprávnění role z kroku 2, která byla podrobně popsána dříve.

### <a name="failover-only"></a>Pouze převzetí služeb při selhání
Chcete-li omezit operace zotavení po havárii až do převzetí služeb při selhání pouze (to znamená bez možností navrácení služeb po obnovení), použijte předchozí postup s těmito výjimkami:

- Místo přiřazení *role Azure_Site_Recovery* k účtu pro přístup virtuálního centra přiřaďte tomuto účtu pouze roli jen *pro čtení.* Tato sada oprávnění umožňuje replikaci virtuálního serveru a převzetí služeb při selhání a neumožňuje navrácení služeb po obnovení.
- Vše ostatní v předchozím procesu zůstává tak, jak je. Chcete-li zajistit izolaci klienta a omezit zjišťování virtuálního hraného, každé oprávnění je stále přiřazena na úrovni objektu je stále přiřazena pouze na úrovni objektu a není šířena do podřízených objektů.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Nasazení prostředků do předplatného klienta

1. Na webu Azure Portal vytvořte skupinu prostředků a pak nasaďte trezor služby Recovery Services podle obvyklého procesu.
2. Stáhněte registrační klíč trezoru.
3. Zaregistrujte CS pro klienta pomocí registračního klíče trezoru.
4. Zadejte pověření pro dva přístupové účty, účet pro přístup k serveru vCenter a účet pro přístup k virtuálnímu účtu.

    ![Účty konfiguračního serveru správce](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Registrace serverů v úschovně

1. Na webu Azure Portal v trezoru, který jste vytvořili dříve, zaregistrujte server vCenter na konfigurační server pomocí účtu vCenter, který jste vytvořili.
2. Dokončete proces "Připravit infrastrukturu" pro obnovení webu podle obvyklého procesu.
3. Virtuální ho virtuální můře jsou teď připravené k replikaci. Ověřte, že se ve**virtuálních počítačích** **Replikovat** > výběr zobrazí jenom virtuální počítače klienta .

## <a name="dedicated-hosting-solution"></a>Vyhrazené hostingové řešení

Jak je znázorněno v následujícím diagramu, architektonický rozdíl ve vyhrazeném hostitelském řešení je, že infrastruktura každého klienta je nastavena pouze pro tohoto klienta.

![architektura-shared-hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Vyhrazený scénář hostování s více vCenters**

## <a name="managed-service-solution"></a>Řešení spravované služby

Jak je znázorněno na následujícím diagramu, architektonický rozdíl v řešení spravované služby je, že infrastruktura každého klienta je také fyzicky oddělena od infrastruktury jiných klientů. Tento scénář obvykle existuje, když klient vlastní infrastrukturu a chce poskytovatele řešení pro správu zotavení po havárii.

![architektura-shared-hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Scénář spravované služby s více vCenters**

## <a name="next-steps"></a>Další kroky
- [Další informace](site-recovery-role-based-linked-access-control.md) o řízení přístupu na základě rolí v site recovery.
- Přečtěte si, jak [nastavit zotavení po havárii virtuálních počítačích VMware do Azure](vmware-azure-tutorial.md).
- Další informace o [víceklientské klienteta s CSP pro virtuální chod vvware VMWare](vmware-azure-multi-tenant-csp-disaster-recovery.md).
