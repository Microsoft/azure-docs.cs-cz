---
title: Přehled podpory více tenantů pro zotavení po havárii virtuálních počítačů VMware do Azure (CSP) pomocí služby Azure Site Recovery | Dokumentace Microsoftu
description: Poskytuje přehled podpory Azure Site Recovery pro zotavení po havárii VMWare do Azure v programu prostředí více tenantů (CSP).
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 2e68ad6d999a5ff003abe35a0cce75bc5f2cebef
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53723922"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>Přehled podpory více tenantů pro zotavení po havárii VMware do Azure s využitím CSP

[Azure Site Recovery](site-recovery-overview.md) podporuje prostředí více tenantů pro předplatná tenanta. Také podporuje více tenantů pro předplatné tenanta, které jsou vytvořeny a spravovány prostřednictvím programu Microsoft Cloud Solution Provider (CSP).

Tento článek poskytuje přehled o implementaci a správu více tenantů replikace z VMware do Azure.

## <a name="multi-tenant-environments"></a>Prostředí s více tenanty

Existují tři hlavní modely víceklientské:

* **Sdílené služby poskytovatele hostingu (HSP)**: Partner vlastní fyzické infrastruktury, a používá sdílené prostředky (vCenter, datová centra, fyzického úložiště a tak dále) pro hostování více klientské virtuální počítače ve stejné infrastruktuře. Partner nabízí správu zotavení po havárii jako spravovanou službu nebo tenanta může vlastnit zotavení po havárii jako řešení samoobslužné služby.

* **Vyhrazené poskytovatele služby hostingu**: Partner vlastní fyzické infrastruktury, ale používá vyhrazené prostředky (více vCenters fyzických úložišť a tak dále) pro hostování virtuálních počítačů každého tenanta do samostatného infrastruktury. Partner nabízí správu zotavení po havárii jako spravovanou službu nebo tenanta může vlastnit jako řešení samoobslužné služby.

* **Spravovat poskytovatele služeb (MSP)**: Zákazník je vlastníkem fyzické infrastruktury, který hostuje virtuální počítače a partnera poskytuje funkce pro kontrolu zotavení po havárii a správu.

## <a name="shared-hosting-services-provider-hsp"></a>Sdílené hostování poskytovatele služeb (HSP)

Dva scénáře jsou podmnožinou tohoto scénáře sdílené hostování a používají stejné zásady. Rozdíly jsou popsány na konci průvodce sdílené hostování.

Základní požadavek ve scénářích s více tenanty je, že klienti musí být izolované. Jednoho tenanta by neměl být schopen sledovat, co má hostovaný jiného tenanta. Tento požadavek není jako důležitý, protože je v samoobslužné prostředí, kde může být rozhodující, v prostředí spravovaná partnerem. Tento článek předpokládá, že je vyžadována izolaci klientů.

Tato architektura se zobrazí v následujícím diagramu.

![Sdílené HSP s jednomu vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**S jedním serverem vCenter hostování sdílených**

V diagramu každý zákazník má samostatné management server. Tato konfigurace omezuje přístup klientů k virtuálním počítačům specifickým pro tenanta a umožňuje izolaci klientů. Replikace virtuálních počítačů VMware používá konfiguračního serveru k vyhledání virtuálních počítačů a nainstalovat agenty. Stejné zásady platí i pro prostředí více tenantů, a uveďte omezení zjišťování virtuálních počítačů pomocí řízení přístupu na serveru vCenter.

Požadavek na izolaci dat znamená, že všechny informace o citlivých infrastruktury (třeba přihlašovací údaje pro přístup) zůstává utajované klientům. Z tohoto důvodu doporučujeme vám, že všechny součásti serveru pro správu nadále výhradní kontrolu partnera. Součásti serveru pro správu jsou:

* Konfigurační server
* Procesový server
* Hlavní cílový server

Samostatný horizontálním navýšením kapacity procesový server je také v partnerské organizace ovládacího prvku.

## <a name="configuration-server-accounts"></a>Konfigurace serveru účty

Každý konfiguračního serveru ve scénáři s více tenanty používá dva účty:

- **účet pro přístup k serveru vCenter**: Tento účet se používá ke zjišťování klientské virtuální počítače. Má přiřazena oprávnění k přístupu vCenter. Pokud chcete vyhnout nevracení přístup, doporučujeme, že partneři zadejte tyto přihlašovací údaje, sami v nástroji pro konfiguraci.

- **Účet pro přístup k virtuální počítač**: Tento účet slouží k instalaci agenta služby Mobility na virtuální počítače, klienta pomocí automatické nabízené oznámení. Obvykle je účet domény, který tenanta může poskytnout s partnerem nebo účet, který partner může spravovat přímo. Pokud klient nechce sdílet podrobnosti přímo s partnerem, mohou zadat přihlašovací údaje prostřednictvím časovým omezením přístupu ke konfiguračnímu serveru. Nebo partnera, podporu, si můžou nainstalovat agenta služby Mobility ručně.

## <a name="vcenter-account-requirements"></a>požadavky na účet vCenter

Nakonfigurujte konfiguračního serveru pomocí účtu, který má zvláštní roli přiřazenou.

- Přiřazení role musí být použitý pro účet pro přístup k vCenter pro každý objekt vCenter a nejsou šířeny do podřízených objektů. Tato konfigurace zajišťuje izolaci klientů, protože šíření přístup může vést k náhodným přístupem na jiné objekty.

    ![Rozšířit na podřízený objekt možnost](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- Alternativním přístupem je přiřazení uživatelského účtu a role na objekt datového centra a jejich šíření do podřízených objektů. Zadejte účet **bez přístupu** role pro každý objekt (jako jsou virtuální počítače, které patří do jiných tenantů), který by ji znepřístupníme pro konkrétního tenanta. Tato konfigurace je náročné. Poskytuje řízení náhodného přístupu, protože každý nový podřízený objekt je také automaticky udělen přístup, který je zděděn z nadřazeného objektu. Proto doporučujeme použít první přístup.

### <a name="create-a-vcenter-account"></a>Vytvořit účet vCenter

1. Vytvoření nové role naklonováním předdefinovaného *jen pro čtení* role a pak poskytněte vhodný název (například Azure_Site_Recovery, jak je znázorněno v tomto příkladu).
2. K této roli přiřadíte následující oprávnění:

    * **Úložiště dat**: Přidělit prostor, procházet úložiště dat, operace se soubory nízké úrovně, odebrat soubor, soubory aktualizace virtuálního počítače
    * **Síť**: Přiřazení sítě
    * **Prostředek**: Přiřazení virtuálního počítače do fondu zdrojů, migrovat vypnutý virtuální počítač, migrovat zapnutý virtuální počítač
    * **Úlohy**: Vytvořit úlohu, aktualizovat úlohu
    * **Virtuálního počítače – konfigurace**: Vše
    - **Virtuální počítač – interakce** > zodpovědět otázky, připojení zařízení, konfigurovat disk CD, konfigurovat disketu, vypnout, zapnout, instalace nástrojů VMware
    - **Virtuální počítač – inventář** > Vytvoření z existujících, vytvořit, zaregistrovat, zrušit registraci
    - **Virtuální počítač – zřizování** > Povolit stažení virtuálního počítače, povolit nahrávání souborů virtuálního počítače
    - **Virtuální počítač – Správa snímku** > odebrat snímky

        ![Dialogové okno Upravit roli](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Přiřaďte úrovně přístupu na účet vCenter (používá se v tenantovi konfigurační server) pro různé objekty, následujícím způsobem:

>| Objekt | Role | Poznámky |
>| --- | --- | --- |
>| vCenter | Jen pro čtení | Potřeba pouze na povolit přístup k vCenter pro správu různé objekty. Můžete odebrat toto oprávnění, pokud nikdy být k dispozici tenanta na spravovaného účtu nebo používá pro všechny operace správy na serveru vCenter. |
>| Datové centrum | Azure_Site_Recovery |  |
>| Hostitel a cluster hostitele | Azure_Site_Recovery | Znovu zajišťuje, že přístup na úrovni objektu tak, aby pouze přístupné hostiteli klientské virtuální počítače před převzetí služeb při selhání a po navrácení služeb po obnovení. |
>| Úložiště dat a úložiště dat clusteru | Azure_Site_Recovery | Stejné jako předchozí. |
>| Síť | Azure_Site_Recovery |  |
>| Server pro správu | Azure_Site_Recovery | Poskytuje přístup pro všechny součásti (CS, PS a MT) mimo počítač CS. |
>| Virtuální počítače tenanta | Azure_Site_Recovery | Zajišťuje, že všechny nové klientské virtuální počítače z konkrétního tenanta také získat přístup nebo nebudou zjistitelné prostřednictvím webu Azure portal. |

Přístup k účtu vCenter je nyní dokončena. Tento krok splňuje požadavek na minimální oprávnění k dokončení operace navrácení služeb po obnovení. Tato oprávnění můžete použít také s existující zásady. Upravte existující oprávnění nastavit, aby zahrnovalo oprávnění role z kroku 2, podrobné dříve.

### <a name="failover-only"></a>Pouze převzetí služeb při selhání
Omezit operace zotavení po havárii až pouze převzetí služeb při selhání (to znamená bez možnosti navrácení služeb po obnovení), pomocí předchozího postupu s následujícími výjimkami:

- Namísto přiřazení *Azure_Site_Recovery* pouze přiřazení role na účet pro přístup k serveru vCenter *jen pro čtení* role k tomuto účtu. Tato sada oprávnění umožňuje replikaci virtuálních počítačů a převzetí služeb při selhání, a neumožňuje navrácení služeb po obnovení.
- Všechno ostatní předchozí probíhající zůstává, dokud je. K zajištění izolaci klientů a omezte zjišťování virtuálních počítačů, každé oprávnění stále přiřazené na úrovni objektu a nejsou šířeny do podřízených objektů.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Nasazení prostředků do tenanta předplatného

1. Na portálu Azure portal vytvořte skupinu prostředků a potom nasazovat trezor služby Recovery Services, obvykle proces.
2. Stáhněte registrační klíč trezoru.
3. Zaregistrujte se na CS tenanta pomocí registračního klíče trezoru.
4. Zadejte pověření pro dva přístupové účty, účet pro přístup k serveru vCenter a účet pro přístup k virtuálnímu počítači.

    ![Účty serveru configuration Manager](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Zaregistrujte server v trezoru

1. Na webu Azure Portal, v trezoru, který jste vytvořili dříve zaregistrovat server vCenter do konfiguračního serveru, vCenter pomocí účtu, který jste vytvořili.
2. Dokončení procesu "Příprava infrastruktury" pro Site Recovery za obvykle procesu.
3. Virtuální počítače jsou nyní připraveny k replikaci. Ověřte, že se zobrazují jenom virtuální počítače tenanta **replikovat** > **výběr virtuálních počítačů**.

## <a name="dedicated-hosting-solution"></a>Vyhrazené řešení v oblasti hostování

Jak je znázorněno v následujícím diagramu, architektury rozdíl ve vyhrazené řešení hostování je, že každý tenant nastavíte infrastrukturu pro tohoto tenanta pouze.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Vyhrazené scénáři hostování s více vCenters**

## <a name="managed-service-solution"></a>Řešení spravovaných služeb

Jak je znázorněno v následujícím diagramu, architektury rozdíl v řešení spravované služby je, že každý tenant infrastruktury i fyzicky oddělená od ostatních tenantů infrastruktury. Tento scénář obvykle existuje, když klient vlastní infrastruktury a chce poskytovatel řešení pro správu zotavení po havárii.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Spravovaná služba scénář s více vCenters**

## <a name="next-steps"></a>Další postup
- [Další informace](site-recovery-role-based-linked-access-control.md) o řízení přístupu na základě rolí ve službě Site Recovery.
- Zjistěte, jak [nastavit zotavení po havárii virtuálních počítačů VMware do Azure](vmware-azure-tutorial.md).
- Další informace o [víceklientské prostředí s CSP pro virtuální počítače VMWare](vmware-azure-multi-tenant-csp-disaster-recovery.md).
