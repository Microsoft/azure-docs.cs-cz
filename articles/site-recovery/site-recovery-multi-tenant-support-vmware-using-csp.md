---
title: "Podpora více klientů pro replikaci virtuálních počítačů VMware do Azure (CSP program) | Microsoft Docs"
description: "Popisuje, jak nasadit Azure Site Recovery v prostředí s více klienty k orchestraci replikace, převzetí služeb při selhání a obnovení virtuálních počítačů (VM) místní VMware do Azure prostřednictvím programu CSP pomocí portálu Azure"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 532dd399d2d5fcbab616744dd02f4a95078cbb1b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-csp"></a>Podpora více klientů ve službě Azure Site Recovery pro replikaci virtuálních počítačů VMware do Azure pomocí zprostředkovatele kryptografických služeb

Azure Site Recovery podporuje víceklientské prostředí odběrů klienta. Podporuje také více klientů pro klienta předplatné, které jsou vytvořeny a spravovat prostřednictvím programu Microsoft Cloud Solution Provider (CSP). Tento článek podrobně popisuje pokyny pro implementaci a správu scénáře víceklientské VMware do Azure. Podrobnosti o vytváření a správa předplatných klienta najdete v tématu [Správa víceklientský s CSP](site-recovery-manage-multi-tenancy-with-csp.md) .

V tomto návodu nevykresluje výraznou z ve stávající dokumentaci pro replikaci virtuálních počítačů VMware do Azure. Další informace najdete v tématu [virtuálních počítačů VMware replikovat do Azure pomocí Site Recovery](site-recovery-vmware-to-azure.md).

## <a name="multi-tenant-environments"></a>Víceklientské prostředí
Existují tři hlavní modely více klientů:

* **Sdílené hostování poskytovatele služeb (HSP)**: partnerovi vlastní fyzické infrastruktuře, a používá sdílených prostředků (vCenter, datových center, fyzického úložiště a tak dále) pro hostování virtuálních počítačů více klienty ve stejné infrastruktuře. Partner můžete zadat zotavení po havárii správu jako spravované služby, nebo klienta můžete vlastní zotavení po havárii jako řešení samoobslužné služby.

* **Vyhrazené hostování zprostředkovatele služeb**: partnerovi vlastní fyzické infrastruktuře, ale využívá k hostování každého klienta virtuálních počítačů na infrastruktuře samostatné vyhrazených prostředcích (více Vcenter, fyzické datastores a tak dále). Partner můžete zadat zotavení po havárii správu jako spravované služby, nebo klienta můžete vlastní jako řešení samoobslužné služby.

* **Spravované služby zprostředkovatele (MSP)**: zákazník vlastní fyzické infrastruktury, který je hostitelem virtuálních počítačů a partnerovi poskytuje povolování zotavení po havárii a pro správu.

## <a name="shared-hosting-multi-tenant-guidance"></a>Sdílené hostování víceklientské pokyny
Tato část popisuje scénář sdílené hostování podrobně. Příslušné dva scénáře jsou podmnožinou tohoto scénáře sdílené hostování a používají stejné zásady. Na konci pokyny sdílené hostování jsou popsané rozdíly.

Základní požadavek ve scénáři více klientů, je izolovat různé klienty. Jeden klient by neměl být schopný sledovat, co má atribut hosted jiného klienta. Tento požadavek není důležité, protože je v samoobslužné prostředí, kde může být rozhodující, v prostředí spravovaná partnerem. Tyto pokyny předpokládají, že je vyžadována izolaci klientů.

Architektura je uvedené v následujícím diagramu:

![Sdílené HSP s jeden vCenter](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)  
**Sdílené hostování scénář se jeden vCenter**

Jak je vidět na předchozím obrázku, každý zákazník má server pro správu samostatné. Tato konfigurace omezuje přístup klientů k virtuálním počítačům konkrétního klienta a umožňuje izolaci klientů. Scénář replikaci virtuálního počítače VMware používá konfigurační server Pokud chcete spravovat účty pro vyhledání virtuálních počítačů a nainstalovat agenty. Stejné zásady platí pro víceklientské prostředí, a uveďte omezení zjišťování virtuálních počítačů prostřednictvím vCenter řízení přístupu.

Požadavek na data izolace vyžaduje, aby zůstala podmínky klientům všechny informace o citlivých infrastruktury (například přihlašovací údaje). Z tohoto důvodu doporučujeme, aby všechny součásti serveru pro správu nadále výhradní řídit partnera. Součásti serveru správy jsou:
* Konfigurační server (CS)
* Procesový server (PS)
* Hlavní cílový server (MT)

PS Škálováním na více systémů je také pod kontrolou partnera.

### <a name="every-cs-in-the-multi-tenant-scenario-uses-two-accounts"></a>Každý CS ve scénáři víceklientské používá dva účty

- **účet pro přístup k systému vCenter**: Tento účet použít ke zjištění klientské virtuální počítače. Má oprávnění k přístupu vCenter přiřazen (jak je popsáno v další části). Abyste se vyhnuli přístup náhodných úniků, doporučujeme, partneři zadali tyto přihlašovací údaje, sami v konfiguračním nástroji služby.

- **Účet pro přístup k virtuálnímu počítači**: Tento účet použít na virtuální počítače klientů pomocí automatické nabízené instalace agenta mobility. Je obvykle účet domény, který klient může poskytovat partnera nebo ten, který partner může spravovat přímo. Pokud klient není chcete sdílet podrobnosti s partnerovi přímo, může být povoleno zadejte přihlašovací údaje prostřednictvím časově omezenou přístupu k CS nebo s jeho pomocí nainstalovat agenty mobility ručně.

### <a name="requirements-for-a-vcenter-access-account"></a>Požadavky pro účet přístupu k systému vCenter

Jak je uvedeno v předchozí části, musíte nakonfigurovat účet, který má speciální role přiřazené CS. Přiřazení role musí být použít účet přístupu k vCenter pro každý objekt vCenter a nebyl rozšířen do podřízených objektů. Tato konfigurace zajišťuje izolaci klientů, vzhledem k šíření přístup může být v náhodných přístup k ostatním objektům.

![Propagate možnosti podřízené objekty](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

Alternativou je přiřazení role v objektu datacenter a uživatelský účet a šířit je do podřízených objektů. Potom nového účtu *žádný přístup* role pro každý objekt (například virtuální počítače ostatních klientů), která má být dostupná pro konkrétního klienta. Tato konfigurace je náročná a zpřístupňuje ovládacích prvků náhodných přístup, protože každý nový podřízený objekt je také automaticky udělen přístup, který dědí z nadřazeného objektu. Proto doporučujeme použít první metodu.

Postup přístup účtu vCenter je následující:

1. Vytvoření nové role klonováním předem definované *jen pro čtení* role a pak mu pohodlný název (například Azure_Site_Recovery, jak je znázorněno v tomto příkladu).

2. K této roli přiřaďte následující oprávnění:

    * **Úložiště dat**: přidělit prostor, procházet úložiště, operace nízké úrovně souborů, odebrat soubor, soubory aktualizace virtuálního počítače

    * **Sítě**: sítě přiřazení

    * **Prostředek**: používá technologii migrací přiřazení virtuálního počítače do fondu zdrojů, migrací vypnout virtuální počítač, na virtuálním počítači

    * **Úlohy**: vytvoření úlohy, úloha aktualizace

    * **Virtuální počítač**:
        * Konfigurace > všechny
        * Interakce > odpovědět na otázku, připojení zařízení, nakonfigurovat CD média, konfigurovat disketová média, vypnutí napájení, instalaci nástroje VMware
        * Inventář > vytvořit z existujícího, vytvořit nový, registraci, zrušení registrace
        * Zřizování > Povolit stahování virtuálního počítače, nahrávání souborů virtuálního počítače povolit
        * Správa snímků > odebrat snímky

    ![Dialogové okno Upravit Role](./media/site-recovery-multi-tenant-support-vmware-using-csp/edit-role-permissions.png)

3. Přiřadíte úrovně přístupu k účtu vCenter (používá se v klientovi CS) pro různé objekty, následujícím způsobem:

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

Chcete-li omezit operace zotavení po havárii, dokud stav převzetí služeb při selhání (to znamená, bez možnosti navrácení služeb po obnovení), postupujte podle předchozích pokynů, s výjimkou: místo přiřazení *Azure_Site_Recovery* pouze přiřazení role účet pro přístup k systému vCenter *jen pro čtení* role k tomuto účtu. Této sadě oprávnění umožňuje replikace virtuálního počítače a převzetí služeb při selhání a navrácení služeb po obnovení není povolen. Všem ostatním v předchozím procesu zůstane, jako je. Každých oprávnění k zajištění izolaci klientů a omezte zjišťování virtuálních počítačů, je stále přidělený pouze na úrovni objekt a není rozšíří do podřízených objektů.

## <a name="other-multi-tenant-environments"></a>Dalších prostředích s více klienty

V předchozí části je popsané, jak nastavit prostředí s více klienty pro sdílené hostování řešení. Další dvě hlavní řešení jsou vyhrazené hostování a spravované služby. Architektura pro těchto řešení je popsána v následujících částech.

### <a name="dedicated-hosting-solution"></a>Vyhrazené řešení v oblasti hostování

Jak je znázorněno v následujícím diagramu, architektury rozdíl ve vyhrazené hostingu řešení je, je pro tohoto klienta pouze nastavili infrastruktury každého klienta. Vzhledem k tomu, že klienti jsou izolované prostřednictvím samostatné Vcenter, poskytovatele hostingu musí stále postupujte podle kroků CSP zadaná pro sdílené hostování, ale není nutné se obávat izolaci klientů. Instalační program zprostředkovatele kryptografických služeb zůstává beze změny.

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/dedicated-hosting-scenario.png)  
**Vyhrazený hostitelský scénář s více Vcenter**

### <a name="managed-service-solution"></a>Řešení spravované služby

Jak je znázorněno v následujícím diagramu, architektury rozdíl v řešení spravované služby je každý klient infrastruktury je také fyzicky oddělená od ostatních klientů infrastruktury. Tento scénář obvykle existuje, když klient vlastní infrastrukturu a chce poskytovatele řešení pro správu zotavení po havárii. Znovu protože klienti jsou fyzicky izolované prostřednictvím různých infrastruktury, je nutné partnera postupujte podle kroků CSP k dispozici pro sdílené hostování ale není nutné se obávat izolaci klientů. Zřizování CSP zůstává beze změny.

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/managed-service-scenario.png)  
**Spravované služby scénář s více Vcenter**

## <a name="next-steps"></a>Další postup
[Další informace](site-recovery-role-based-linked-access-control.md) o řízení přístupu na základě rolí pro správu nasazení Azure Site Recovery.

[Správa více klientů s zprostředkovatele kryptografických služeb](site-recovery-manage-multi-tenancy-with-csp.md)
