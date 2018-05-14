---
title: Přehled podpory víceklientské pro replikaci virtuálního počítače VMware Azure (CSP) pomocí Azure Site Recovery | Microsoft Docs
description: Poskytuje přehled o Azure Site Recovery podporu pro klienta odběry v prostředí s více klienty, prostřednictvím programu CSP.
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 0168849c01add3f714b139c7984e3def74f40a5b
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="overview-of-multi-tenant-support-for-vmware-replication-to-azure-with-csp"></a>Přehled podpory víceklientské pro replikace VMware do Azure pomocí zprostředkovatele kryptografických služeb

[Azure Site Recovery](site-recovery-overview.md) podporuje víceklientské prostředí odběrů klienta. Podporuje také více klientů pro klienta předplatné, které jsou vytvořeny a spravovat prostřednictvím programu Microsoft Cloud Solution Provider (CSP).

Tento článek obsahuje přehled implementace a správa víceklientské VMware do Azure replikace.

## <a name="multi-tenant-environments"></a>Víceklientské prostředí

Existují tři hlavní modely více klientů:

* **Sdílené hostování poskytovatele služeb (HSP)**: partnerovi vlastní fyzické infrastruktuře, a používá sdílených prostředků (vCenter, datových center, fyzického úložiště a tak dále) pro hostování více klientské virtuální počítače ve stejné infrastruktuře. Partner můžete zadat zotavení po havárii správu jako spravované služby, nebo klienta můžete vlastní zotavení po havárii jako řešení samoobslužné služby.

* **Vyhrazené hostování zprostředkovatele služeb**: partnerovi vlastní fyzické infrastruktuře, ale využívá k hostování každého klienta virtuálních počítačů na infrastruktuře samostatné vyhrazených prostředcích (více Vcenter, fyzické datastores a tak dále). Partner můžete zadat zotavení po havárii správu jako spravované služby, nebo klienta můžete vlastní jako řešení samoobslužné služby.

* **Spravované služby zprostředkovatele (MSP)**: zákazník vlastní fyzické infrastruktury, který je hostitelem virtuálních počítačů a partnerovi poskytuje povolování zotavení po havárii a pro správu.

## <a name="shared-hosting-services-provider-hsp"></a>Sdílené hostování zprostředkovatele služeb (HSP)

Příslušné dva scénáře jsou podmnožinou tohoto scénáře sdílené hostování a používají stejné zásady. Na konci pokyny sdílené hostování jsou popsané rozdíly.

Základní požadavek ve scénáři více klientů je, že je klientům izolované. Jeden klient by neměl být schopný sledovat, co má atribut hosted jiného klienta. Tento požadavek není důležité, protože je v samoobslužné prostředí, kde může být rozhodující, v prostředí spravovaná partnerem. Tento článek předpokládá, že je vyžadována izolaci klientů.

Architektura je znázorněno v následujícím diagramu.

![Sdílené HSP s jeden vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**S jedním serverem vCenter hostování sdílených**

V diagramu každou zákazník má server pro správu samostatné. Tato konfigurace omezuje přístup klientů k virtuálním počítačům konkrétního klienta, a umožňuje izolaci klientů. Replikace virtuálních počítačů VMware používá konfigurační server k vyhledání virtuálních počítačů a nainstalovat agenty. Stejné zásady platí pro víceklientské prostředí, a uveďte omezení zjišťování virtuálních počítačů pomocí řízení přístupu vCenter.

Požadavek na izolaci dat znamená, že všechny infrastruktury citlivé informace (například přihlašovací údaje) zůstane podmínky klientům. Z tohoto důvodu doporučujeme, aby všechny součásti serveru pro správu nadále výhradní řídit partnera. Součásti serveru správy jsou:

* Konfigurace serveru
* Procesový server
* Hlavní cílový server

Samostatný upraveným procesový server je také pod kontrolou partnera.

## <a name="configuration-server-accounts"></a>Konfigurace serveru účty

Každý server konfiguraci ve víceklientské scénáři používá dva účty:

- **účet pro přístup k systému vCenter**: Tento účet se používá ke zjišťování klientské virtuální počítače. Má oprávnění k přístupu vCenter přiřazen. Abyste se vyhnuli nevracení přístup, doporučujeme, partneři zadali tyto přihlašovací údaje, sami v konfiguračním nástroji služby.

- **Účet pro přístup k virtuálnímu počítači**: Tento účet se používá k instalaci agenta služby Mobility na klientské virtuální počítače, pomocí automatické nabízené. Obvykle je účet domény, který klient může poskytovat partnera nebo účet, který partner může spravovat přímo. Pokud klient není chcete sdílet podrobnosti s partnerovi přímo, můžou zadat přihlašovací údaje prostřednictvím přístupu časově omezenou na konfiguračním serveru. Nebo s jeho pomocí se můžete nainstalovat agenta služby Mobility ručně.

## <a name="vcenter-account-requirements"></a>požadavky na účet vCenter

Konfigurační server nakonfigurujte účet, který má roli speciální přiřazen.

- Přiřazení role musí být použít účet přístupu k vCenter pro každý objekt vCenter a nebyl rozšířen do podřízených objektů. Tato konfigurace zajišťuje izolaci klientů, vzhledem k šíření přístup může být v náhodných přístup k ostatním objektům.

    ![Propagate možnosti podřízené objekty](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- Alternativní způsob je přiřazení role v objektu datacenter a uživatelský účet a šířit je do podřízených objektů. Potom nového účtu **žádný přístup** role pro každý objekt (například virtuální počítače, které patří do jiných klientů), by měla být dostupná pro konkrétního klienta. Tato konfigurace je náročná. Zpřístupňuje náhodných přístup ovládacích prvků, protože každý nový podřízený objekt je také automaticky udělen přístup, který dědí z nadřazeného objektu. Proto doporučujeme použít první metodu.

### <a name="create-a-vcenter-account"></a>Vytvoření účtu vCenter

1. Vytvoření nové role klonováním předdefinovanou *jen pro čtení* role a pak mu pohodlný název (například Azure_Site_Recovery, jak je znázorněno v tomto příkladu).
2. K této roli přiřaďte následující oprávnění:

    * **Úložiště dat**: přidělit prostor, procházet úložiště, operace nízké úrovně souborů, odebrat soubor, soubory aktualizace virtuálního počítače
    * **Sítě**: sítě přiřazení
    * **Prostředek**: používá technologii migrací přiřazení virtuálního počítače do fondu zdrojů, migrací vypnout virtuální počítač, na virtuálním počítači
    * **Úlohy**: vytvoření úlohy, úloha aktualizace
    * **Virtuální počítač – konfigurační**: všechny
    - **Virtuální počítač – interakce** > odpovědět na otázku, připojení zařízení, nakonfigurovat CD média, konfigurovat disketová média, vypnutí napájení, instalaci nástroje VMware
    - **Virtuální počítač – inventáře** > vytvořit z existujícího, vytvořit nový, registraci, zrušení registrace
    - **Virtuální počítač – zřizování** > Povolit stahování virtuálního počítače, nahrávání souborů virtuálního počítače povolit
    - **Virtuální počítač – Správa snímků** > odebrat snímky

        ![Dialogové okno Upravit Role](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Přiřaďte úrovně přístupu k účtu vCenter (používá se v klientovi konfigurační server) pro různé objekty, následujícím způsobem:

>| Objekt | Role | Poznámky |
>| --- | --- | --- |
>| vCenter | Jen pro čtení | Potřeba pouze pro povolení přístupu vCenter pro správu různé objekty. Toto oprávnění může odebrat, pokud účet nikdy bude potřeba poskytnout klienta nebo použít pro žádné operace správy na serveru vCenter. |
>| Datové centrum | Azure_Site_Recovery |  |
>| Hostitele a cluster hostitelů | Azure_Site_Recovery | Znovu zajišťuje, že přístup na úrovni objektu, tak, aby pouze přístupné hostitelů klientské virtuální počítače před převzetí služeb při selhání a po navrácení služeb po obnovení. |
>| Úložiště dat a úložiště clusteru | Azure_Site_Recovery | Stejné jako předcházející. |
>| Síť | Azure_Site_Recovery |  |
>| Server pro správu | Azure_Site_Recovery | Poskytuje přístup k všechny součásti (CS, PS a MT) mimo počítač CS. |
>| Virtuální počítače klienta | Azure_Site_Recovery | Zajišťuje, že všechny nové klientské virtuální počítače konkrétního klienta také získat tento přístup, nebo nebude zjistitelný prostřednictvím portálu Azure. |

Přístup k účtu vCenter je nyní dokončen. Tento krok splní požadavek na minimální oprávnění k dokončení operace navrácení služeb po obnovení. Můžete taky těchto oprávnění k přístupu s existující zásady. Právě upravte vaše stávající oprávnění nastavit, aby zahrnovalo oprávnění role z kroku 2, podrobné dříve.

### <a name="failover-only"></a>Pouze převzetí služeb při selhání
Omezit operace zotavení po havárii, až se pouze převzetí služeb při selhání (tedy bez možnosti navrácení služeb po obnovení), použít předchozí postup, s těmito výjimkami:

- Místo přiřazení *Azure_Site_Recovery* role účet pro přístup k systému vCenter přiřadit jenom *jen pro čtení* role k tomuto účtu. Této sadě oprávnění umožňuje replikace virtuálního počítače a převzetí služeb při selhání a navrácení služeb po obnovení není povolen.
- Všem ostatním v předchozím procesu zůstane, jako je. K zajištění izolaci klientů a omezte zjišťování virtuálních počítačů, každý oprávnění stále přidělený pouze na úrovni objekt a není rozšíří do podřízených objektů.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Nasadit prostředky k předplatnému klienta

1. Na portálu Azure vytvořte skupinu prostředků a pak nasadit trezoru služeb zotavení pro obvyklé procesy.
2. Stáhněte registrační klíč trezoru.
3. Zaregistrujte CS pro klienta pomocí registračního klíče trezoru.
4. Zadejte pověření pro dva přístupové účty, účet pro přístup k serveru vCenter a účet přístup k virtuálnímu počítači.

    ![Účty serveru configuration Manager](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Zaregistrujte server v trezoru

1. Na portálu Azure v úložišti, který jste vytvořili dříve zaregistrujte server vCenter pro konfigurační server, pomocí účtu vCenter, který jste vytvořili.
2. Dokončení procesu "Připravit infrastrukturu" pro obnovení lokality podle obvyklé procesu.
3. Virtuální počítače jsou nyní připraveny k replikaci. Ověřte, že se zobrazují jenom virtuální počítače klienta v **replikovat** > **vybrat virtuální počítače**.

## <a name="dedicated-hosting-solution"></a>Vyhrazené řešení v oblasti hostování

Jak je znázorněno v následujícím diagramu, architektury rozdíl ve vyhrazené hostingu řešení je, je pro tohoto klienta pouze nastavili infrastruktury každého klienta.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Vyhrazený hostitelský scénář s více Vcenter**

## <a name="managed-service-solution"></a>Řešení spravované služby

Jak je znázorněno v následujícím diagramu, architektury rozdíl v řešení spravované služby je každý klient infrastruktury je také fyzicky oddělená od ostatních klientů infrastruktury. Tento scénář obvykle existuje, když klient vlastní infrastrukturu a chce poskytovatele řešení pro správu zotavení po havárii.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Spravované služby scénář s více Vcenter**

## <a name="next-steps"></a>Další postup
- [Další informace](site-recovery-role-based-linked-access-control.md) o řízení přístupu na základě role ve službě Site Recovery.
- Zjistěte, jak [nastavit zotavení po havárii virtuálních počítačů VMware do Azure](vmware-azure-tutorial.md).
- Další informace o [víceklientský s zprostředkovatele kryptografických služeb pro virtuální počítače VMWare](vmware-azure-multi-tenant-csp-disaster-recovery.md).
