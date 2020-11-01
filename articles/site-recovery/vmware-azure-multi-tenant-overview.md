---
title: Zotavení virtuálních počítačů VMware s více klienty pomocí Azure Site Recovery
description: V této části najdete přehled podpory Azure Site Recovery pro zotavení po havárii VMWare do Azure v programu pro více tenantů prostředí (CSP).
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 64e40341ec56a2e1c561b2bcbb5e584830c14015
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145578"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>Přehled podpory více tenantů pro zotavení po havárii VMware do Azure pomocí CSP

[Azure Site Recovery](site-recovery-overview.md) podporuje víceklientské prostředí pro předplatná klientů. Podporuje taky víceklientské architektury pro předplatná tenantů, která jsou vytvořená a spravovaná prostřednictvím programu Microsoft Cloudho poskytovatele řešení (CSP).

Tento článek poskytuje přehled implementace a správy víceklientské architektury VMware do Azure.

## <a name="multi-tenant-environments"></a>Víceklientské prostředí

Existují tři hlavní modely s více klienty:

* **Poskytovatel sdílených hostitelských služeb (HSP)** : partner vlastní fyzickou infrastrukturu a používá sdílené prostředky (vCenter, datacentra, fyzické úložiště atd.) k hostování více virtuálních počítačů tenantů ve stejné infrastruktuře. Partner může jako spravovanou službu poskytnout správu zotavení po havárii, nebo může klient vlastní zotavení po havárii jako samoobslužné řešení.

* **Vyhrazený poskytovatel hostitelských služeb** : partner vlastní fyzickou infrastrukturu, ale využívá vyhrazené prostředky (více servery vCenter, fyzických úložišť dat atd.) k hostování virtuálních počítačů jednotlivých tenantů na samostatné infrastruktuře. Partner může jako spravovanou službu poskytnout správu zotavení po havárii, nebo může tento tenant vlastnit jako samoobslužné řešení.

* **Zprostředkovatel spravovaných služeb (MSP)** : zákazník vlastní fyzickou infrastrukturu, která je hostitelem virtuálních počítačů, a partner poskytuje povolení a správu zotavení po havárii.

## <a name="shared-hosting-services-provider-hsp"></a>Zprostředkovatel sdíleného hostování služeb (HSP)

Ostatní dva scénáře jsou podmnožinou scénáře sdíleného hostování a používají stejné principy. Rozdíly jsou popsány na konci pokynů pro sdílení hostování.

Základním požadavkem ve víceklientském scénáři je, že klienti musí být izolované. Jeden tenant by neměl být schopný sledovat, co je hostitelem jiného tenanta. V prostředí spravovaném partnerem není tento požadavek tak důležitý, protože je v prostředí samoobslužné služby, kde může být kritický. V tomto článku se předpokládá, že je potřeba izolace tenanta.

Architektura se zobrazuje v následujícím diagramu.

![Sdílené HSP s jedním vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Sdílené – hostování s jedním serverem vCenter**

V diagramu má každý zákazník samostatné management server. Tato konfigurace omezuje přístup klienta k virtuálním počítačům specifickým pro tenanta a umožňuje izolaci tenanta. Replikace virtuálních počítačů VMware používá konfigurační server ke zjišťování virtuálních počítačů a instalaci agentů. Stejné zásady platí i pro víceklientské prostředí s přidáním omezení zjišťování virtuálních počítačů pomocí řízení přístupu vCenter.

Požadavek na izolaci dat znamená, že všechny citlivé informace o infrastruktuře (například přihlašovací údaje) zůstávají pro klienty nezveřejňované. Z tohoto důvodu doporučujeme, aby všechny součásti management server zůstaly pod výhradním ovládacím prvkem partnera. Komponenty management server jsou:

* Konfigurační server
* Procesový Server
* Hlavní cílový server

Samostatný procesový Server se škálováním na více instancí je také pod řízením partnera.

## <a name="configuration-server-accounts"></a>Účty konfiguračního serveru

Každý konfigurační server ve scénáři s více klienty používá dva účty:

- **účet pro přístup k serveru vCenter** : Tento účet se používá ke zjišťování virtuálních počítačů tenanta. Má přiřazená přístupová oprávnění vCenter. Aby se zabránilo nevracení přístupu, doporučujeme, aby partneři tyto přihlašovací údaje zadali sami v nástroji pro konfiguraci.

- **Účet pro přístup k virtuálnímu počítači** : Tento účet se používá k instalaci agenta služby mobility na virtuální počítače tenantů s automatickým vložením. Obvykle se jedná o doménový účet, který může klient poskytnout partnerovi, nebo účet, který může partner spravovat přímo. Pokud tenant nechce sdílet podrobnosti s partnerem přímo, můžou přihlašovací údaje zadat prostřednictvím přístupu ke konfiguračnímu serveru v omezeném čase. Nebo s asistencí partnera může nainstalovat agenta služby mobility ručně.

## <a name="vcenter-account-requirements"></a>požadavky na účet vCenter

Nakonfigurujte konfigurační server pomocí účtu, který má přiřazenou zvláštní roli.

- Přiřazení role se musí použít pro účet pro přístup vCenter pro každý objekt vCenter a nešíří se do podřízených objektů. Tato konfigurace zajišťuje izolaci tenanta, protože šíření přístupu může mít za následek náhodný přístup k jiným objektům.

    ![Možnost rozšířit na podřízené objekty](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- Alternativním přístupem je přiřazení uživatelského účtu a role k objektu datacentra a jejich šíření do podřízených objektů. Pak mu udělte účet **bez role přístupu** pro každý objekt (například virtuální počítače, které patří do jiných tenantů), které by měly být pro konkrétního tenanta nepřístupné. Tato konfigurace je nenáročný. Zpřístupňuje nechtěné řízení přístupu, protože každému novému podřízenému objektu je také automaticky udělen přístup, který je zděděný z nadřazené položky. Proto doporučujeme použít první přístup.

### <a name="create-a-vcenter-account"></a>Vytvoření účtu vCenter

1. Vytvořte novou roli klonováním předdefinované role *jen pro čtení* a pak jí poskytněte pohodlný název (například Azure_Site_Recovery, jak je znázorněno v tomto příkladu).
2. Přiřaďte této roli tato oprávnění:

   * **Úložiště dat** : přidělit prostor, procházet úložiště dat, operace se soubory na nízké úrovni, odebrat soubor, aktualizovat soubory virtuálního počítače
   * **Síť** : přiřazení sítě
   * **Prostředek** : přiřazení virtuálního počítače ke fondu zdrojů, migrace napájení z virtuálního počítače a migrace na virtuálním počítači
   * **Úlohy** : vytvořit úlohu, aktualizovat úlohu
   * **Konfigurace virtuálního počítače** : vše
   * **Virtuální počítač – interakce** > odpověď, připojení zařízení, konfigurace média CD, konfigurace disketových médií, zapnutí, zapnutí, instalace nástrojů VMware
   * **Virtuální počítač-inventář** > vytvořit z existujícího, vytvořit nové, registrovat, zrušit registraci
   * **Zřizování virtuálních počítačů –** > povolení stahování virtuálního počítače, povolení nahrávání souborů virtuálního počítače
   * **Virtuální počítač – Správa snímků** > odebírání snímků

       ![Dialogové okno Upravit roli](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Přiřaďte úrovně přístupu k účtu vCenter (použitému na konfiguračním serveru klienta) pro různé objekty následujícím způsobem:

>| Objekt | Role | Poznámky |
>| --- | --- | --- |
>| vCenter | Read-Only | Potřeba jenom k tomu, aby byl přístup vCenter povolený pro správu různých objektů. Toto oprávnění můžete odebrat, pokud se účet nikdy neposkytne klientovi nebo se nepoužije pro žádné operace správy na vCenter. |
>| Datacentrum | Azure_Site_Recovery |  |
>| Hostitelský cluster a hostitelský cluster | Azure_Site_Recovery | Znovu zajistěte, aby byl přístup na úrovni objektu, aby k převzetí služeb při selhání a po navrácení služeb po obnovení měly virtuální počítače tenanta jenom přístupné hostitele. |
>| Úložiště dat a cluster úložiště dat | Azure_Site_Recovery | Stejné jako předchozí. |
>| Síť | Azure_Site_Recovery |  |
>| Server pro správu | Azure_Site_Recovery | Zahrnuje přístup ke všem součástem (CS, PS a MT) mimo počítač CS. |
>| Virtuální počítače tenanta | Azure_Site_Recovery | Zajistí, aby se tento přístup získal taky u všech nových virtuálních počítačů tenanta určitého tenanta, nebo tyto virtuální počítače nebudou zjistitelné prostřednictvím Azure Portal. |

Přístup k účtu vCenter je teď dokončený. Tento krok splňuje požadavky na minimální oprávnění k dokončení operací navrácení služeb po obnovení. Tato přístupová oprávnění můžete také použít u svých existujících zásad. Stačí upravit existující sadu oprávnění tak, aby zahrnovala oprávnění role z kroku 2, popsaná výše.

### <a name="failover-only"></a>Pouze převzetí služeb při selhání
Chcete-li omezit operace zotavení po havárii do režimu pouze pro převzetí služeb při selhání (to znamená bez možnosti navrácení služeb po obnovení), použijte předchozí postup s těmito výjimkami:

- Místo přiřazení role *Azure_Site_Recovery* k účtu pro přístup k serveru vCenter přiřaďte k tomuto účtu pouze roli jen *pro čtení* . Tato sada oprávnění umožňuje replikaci virtuálních počítačů a převzetí služeb při selhání a neumožňuje navrácení služeb po obnovení.
- Vše ostatní v předchozím procesu zůstane tak, jak je. Aby bylo zajištěno izolaci tenanta a omezení zjišťování virtuálních počítačů, jsou všechna oprávnění stále přiřazena pouze na úrovni objektu a nejsou šířena do podřízených objektů.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Nasazení prostředků do předplatného tenanta

1. V Azure Portal vytvořte skupinu prostředků a potom nasaďte Recovery Services trezor na běžný proces.
2. Stáhněte registrační klíč trezoru.
3. Zaregistrujte CS pro tenanta pomocí registračního klíče trezoru.
4. Zadejte přihlašovací údaje pro tyto dva účty pro přístup, účet pro přístup k serveru vCenter a účet pro přístup k virtuálnímu počítači.

    ![Účty konfiguračního serveru správce](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Registrace serverů v trezoru

1. V Azure Portal v trezoru, který jste vytvořili dříve, zaregistrujte vCenter Server na konfigurační server pomocí účtu vCenter, který jste vytvořili.
2. Dokončete proces "Příprava infrastruktury" pro Site Recovery na běžný proces.
3. Virtuální počítače jsou teď připravené k replikaci. Ověřte, že se v části **replikovat**  >  **vybrané virtuální počítače** zobrazují jenom virtuální počítače tenanta.

## <a name="dedicated-hosting-solution"></a>Vyhrazené řešení hostování

Jak je znázorněno v následujícím diagramu, je rozdíl architektury v rámci vyhrazeného hostujícího řešení v tom, že je pro tohoto tenanta nastavena infrastruktura pro jednotlivé klienty.

![Diagram znázorňující rozdíl architektury v rámci vyhrazeného hostujícího řešení je, že pro tohoto tenanta je nastavena infrastruktura pro každého tenanta.](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Scénář vyhrazeného hostování s více servery vCenter**

## <a name="managed-service-solution"></a>Řešení spravované služby

Jak je znázorněno v následujícím diagramu, rozdíl architektury v řešení spravované služby spočívá v tom, že infrastruktura každého tenanta je také fyzicky oddělená od infrastruktury ostatních tenantů. Tento scénář je obvykle k dispozici v případě, že tenant vlastní infrastrukturu a chce poskytovatele řešení spravovat zotavení po havárii.

![Architektura – Shared-HSP](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Scénář spravované služby s více servery vCenter**

## <a name="next-steps"></a>Další kroky
- [Přečtěte si další informace](site-recovery-role-based-linked-access-control.md) o řízení přístupu na základě role v Site Recovery.
- Přečtěte si, jak [nastavit zotavení po havárii virtuálních počítačů VMware do Azure](vmware-azure-tutorial.md).
- Přečtěte si další informace o víceklientské [architektuře s CSP pro virtuální počítače VMware](vmware-azure-multi-tenant-csp-disaster-recovery.md).
