---
title: Zákazník Lockboxu pro Microsoft Azure
description: Technický přehled Lockboxu zákazníka pro Microsoft Azure, která umožňuje řídit přístup poskytovatele cloudu, až Microsoft může potřebovat přístup k zákaznickým datům.
author: cabailey
ms.service: security
ms.topic: article
ms.author: cabailey
manager: barbkess
ms.date: 05/07/2019
ms.openlocfilehash: 468e392cd2c45d79cbb24f8d737a6e83fbcd2725
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079267"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Zákazník Lockboxu pro Microsoft Azure

> [!NOTE]
> Chcete-li tuto funkci používat, musí mít vaše organizace [plán podpory Azure](https://azure.microsoft.com/support/plans/) s minimální úroveň **Developer**.

Lockboxu zákazníka pro Microsoft Azure poskytuje rozhraní pro zákazníky, chcete-li zkontrolovat a schválit nebo odmítnout žádosti o zákaznická data přístup. Používá se v případech, kde je potřeba přístup k zákaznickým datům během žádosti o podporu Microsoftu.

Tento článek popisuje, jak jsou požadavky zákazníka Lockboxu iniciované, sledovat a ukládají pro pozdější revize a auditů.

Zákazník Lockbox je teď obecně dostupná a aktuálně zapnuté pro přístup ke vzdálené ploše pro virtuální počítače.

## <a name="workflow"></a>Pracovní postup

Následující kroky popisují typický pracovní postup pro žádost o Lockboxu zákazníka.

1. Uživatel v organizaci má problém s jejich úlohy Azure.

2. Po tuto osobu ho. problém, ale nelze opravit, otevření lístku podpory od [webu Azure portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). -The-ticket je přiřazen k pracovník podpory zákazníků Azure.

3. Pracovník podpory Azure kontroly žádosti o službu a určuje další kroky k vyřešení daného problému.

4. Pokud pracovník podpory nelze vyřešit tento problém s využitím standardních nástrojů a telemetrie, dalším krokem je žádost o zvýšenou úroveň oprávnění s využitím služby přístup za běhu (JIT). Tento požadavek se dá z původní pracovníkem technické podpory. Nebo to může být z různých technik, protože tento problém je eskalován jej tým Azure DevOps.

5. Po přístupu je odeslána žádost pracovníkem Azure Just-In-Time služba vyhodnocuje požadavek s ohledem na účet faktorů, jako:
    - Rozsah zdroje
    - Určuje, zda je žadateli izolované identity nebo pomocí služby Multi-Factor authentication
    - Úrovně oprávnění
    
    Podle pravidla JIT, tuto žádost mohou zahrnovat také schvalování z interní Microsoft schvalovatele. Schvalovatel může být například vedoucí podpory zákazníků nebo manažer vývoje.

6. Pokud požadavek vyžaduje přímý přístup k zákaznickým datům, je zahájen požadavek Lockboxu zákazníka. Například přístup přes vzdálenou plochu k virtuálnímu počítači zákazníka.
    
    Žádost se teď **odběratele oznámení** stavu čekání na schválení zákazníka před udělením přístupu.

7. V organizaci zákazníka, uživatele, který má [role vlastníka](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) pro Azure odběr přijímá od Microsoftu, a oznámení o žádosti o přístup čeká na e-mailu. Tato osoba pro požadavky zákazníka Lockbox je určené schvalovatele.
    
    Příklad e-mailu:
    
    ![Azure zákazníka Lockboxu - e-mailové oznámení](./media/azure-customer-lockbox/customer-lockbox-email-notification.png)

8. E-mailové oznámení s odkazem na **zákazníka Lockboxu** okna na webu Azure Portal. Pomocí tohoto odkazu, určené schvalovatel přihlásí k webu Azure portal zobrazíte všechny čekající žádosti, které jejich organizace má pro zákazníka Lockboxu:
    
    ![Azure zákazníka Lockboxu – cílová stránka](./media/azure-customer-lockbox/customer-lockbox-landing-page.png)
    
   Požadavek zůstává ve frontě zákazníka pro čtyři dny. Po uplynutí této doby žádost o přístup automaticky vyprší platnost a bez přístupu jsou udělena pro odborníky z Microsoftu.

9. Podrobnosti nevyřízené žádosti získáte určené schvalovatele můžete vybrat lockboxu žádost od **žádosti čekající na vyřízení**:
    
    ![Azure zákazníka Lockboxu – zobrazení čekající na vyřízení žádosti](./media/azure-customer-lockbox/customer-lockbox-pending-requests.png)

10. Můžete také vybrat určené schvalovatel **ID žádosti o služby** Chcete-li zobrazit žádost o podporu lístků, který byl vytvořen uživatelem původní. Tyto informace poskytuje kontext pro Proč je Microsoft Support zapojení a historie ohlášených potíží. Příklad:
    
    ![Azure zákazníka Lockboxu – zobrazení lístku žádosti o podporu](./media/azure-customer-lockbox/customer-lockbox-support-ticket.png)

11. Prohlédněte si požadavek, vybere určené schvalovatel **schválit** nebo **Odepřít**:
    
    ![Azure zákazníka Lockboxu – vyberte možnost schválit nebo odepřít](./media/azure-customer-lockbox/customer-lockbox-approval.png)
    
    V důsledku výběru:
    - **Schválit**:  Technický pracovník Microsoftu je udělen přístup. Přístup se udělí pro výchozí dobu 8 hodin.
    - **Odepřít**: Odmítl žádost o přístup se zvýšeným oprávněním pomocí technický pracovník Microsoftu a nebyla provedena žádná další akce.

Pro účely auditování akce prováděné v tomto pracovním postupu přihlášení [Lockboxu zákazníka žádost o protokoly](#auditing-logs).

## <a name="auditing-logs"></a>Protokoly auditování

Zákazník Lockboxu protokoly se ukládají v protokolech aktivit. Na webu Azure Portal, vyberte **protokoly aktivit** zobrazíte auditní informace související s požadavky zákazníků Lockboxu. Můžete filtrovat na konkrétní akce, jako například:
- **Zamítnout žádost o Lockboxu**
- **Vytvořit žádost o Lockboxu**
- **Schvalte žádost o Lockboxu**
- **Vypršení platnosti požadavku Lockboxu**

Jako příklad:

![Azure zákazníka Lockboxu – protokoly aktivit](./media/azure-customer-lockbox/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios"></a>Podporované služby a scénáře

Následující služby a scénáře jsou aktuálně obecné dostupnosti pro zákazníka Lockboxu.

### <a name="remote-desktop-access-to-virtual-machines"></a>Přístup ke vzdálené ploše pro virtuální počítače

Zákazník Lockbox je zapnut pro přístup přes vzdálenou plochu žádostí směrovaných na virtuální počítače. Podporují se následující úlohy:
- Platforma jako služba (PaaS) – verze 1
- Infrastruktura jako služba (IaaS) – Windows a Linux (pouze Azure Resource Manager)
- Škálovací sada virtuálních počítačů – Windows a Linux

> [!NOTE]
> IaaS Classic instance nepodporuje Lockboxu zákazníka. Pokud máte pracovní postupy spouštěné v instancích IaaS Classic, doporučujeme že vám migrace z modelu Classic na modely nasazení Resource Manager. Pokyny najdete v tématu [platformou podporované migraci prostředků IaaS z modelu classic na Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Podrobné protokoly auditování

Pro scénáře, které zahrnují přístup ke vzdálené ploše vám pomůže protokoly událostí Windows zkontrolujte akce, za kterou technický pracovník Microsoftu. Zvažte použití Azure Security Center ke shromažďování protokolů událostí a zkopírovat data do pracovního prostoru pro analýzu. Další informace najdete v tématu [shromažďování dat ve službě Azure Security Center](../security-center/security-center-enable-data-collection.md).

## <a name="exclusions"></a>Vyloučení

Lockboxu požadavky zákazníků se aktivuje v následujících scénářích technické podpory:

- Technický pracovník Microsoftu je potřeba provést aktivitu, která spadá mimo standardní pracovní postupy. Chcete-li například obnovit nebo obnovení služeb v neočekávané prudkým nárůstem nebo nepředvídatelnými scénáře.

- Technický pracovník Microsoftu přistupuje k nim na platformě Azure jako součást řešení potíží a neúmyslně má přístup k zákaznickým datům. Například tým sítě Azure provádí, řešení potíží, jehož výsledkem zachytávání paketů na síťové zařízení. Nicméně pokud zákazník šifrovaná data, přestože byl při přenosu, inženýr nelze číst data.

## <a name="next-steps"></a>Další postup

Zákazník Lockbox je automaticky dostupný pro všechny zákazníky, kteří mají [plán podpory Azure](https://azure.microsoft.com/support/plans/) s minimální úroveň **Developer**.

Pokud máte plán podpory nárok, nic nevyžaduje se povolit Lockboxu zákazníka. Požadavky zákazníků z Lockboxu lze inicializovat pomocí technický pracovník Microsoftu, pokud tato akce je vyžadována neodpovídajících lístek podpory, který se zaznamená chyba z někdo ve vaší organizaci.
