---
title: Ochrana duševního vlastnictví poskytovatele spravovaného zabezpečení (MSSPs) ve službě Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak můžou Poskytovatelé spravovaných služeb zabezpečení (MSSPs) chránit duševní vlastnictví, které vytvořila v Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2021
ms.author: bagol
ms.openlocfilehash: 1c15c341d85fae667ee23883043350340ad866b8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315407"
---
# <a name="protecting-mssp-intellectual-property-in-azure-sentinel"></a>Ochrana duševního vlastnictví MSSP v Azure Sentinel

Tento článek popisuje metody, které mohou spravované poskytovatelé zabezpečení (MSSPs) využít k ochraně duševního vlastnictví, které vyvinula služba Azure Sentinel, jako jsou pravidla analýzy Azure Sentinel, lovecké dotazy, playbooky a sešity.

Zvolená metoda bude záviset na tom, jak každý z vašich zákazníků kupuje Azure. Ať už pracujete jako [poskytovatel Cloud Solution Provider (CSP)](#cloud-solutions-providers-csp), nebo má zákazník účet [smlouva Enterprise (EA)/Pay-as-you-go (PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg) . Následující části popisují každou z těchto metod samostatně.

## <a name="cloud-solutions-providers-csp"></a>Poskytovatelé cloudových řešení (CSP)

Pokud předáváte Azure jako poskytovatele Cloud Solution Provider (CSP), spravujete předplatné Azure zákazníka. Díky [administrate (admin-on-of)](/partner-center/azure-plan-manage)se uživatelům ve skupině agenti správce z vašeho tenanta mssp uděluje přístup k předplatnému Azure zákazníka a zákazník nemá ve výchozím nastavení přístup.

Pokud jiní uživatelé z tenanta MSSP, mimo skupinu agentů pro správu, potřebují přístup k prostředí zákazníka, doporučujeme, abyste používali [Azure Lighthouse](multiple-tenants-service-providers.md). Azure Lighthouse umožňuje udělit uživatelům nebo skupinám přístup k určitému oboru, jako je například skupina prostředků nebo předplatné, pomocí jedné z předdefinovaných rolí.

Pokud potřebujete poskytnout uživatelům zákazníka přístup k prostředí Azure, doporučujeme udělit jim přístup na úrovni *skupiny prostředků* namísto celého předplatného, takže můžete podle potřeby zobrazit nebo skrýt části prostředí.

Například:

- Zákazníkům můžete udělit přístup k několika skupinám prostředků, kde se nacházejí jejich aplikace, ale stále udržují pracovní prostor Azure Sentinel v samostatné skupině prostředků, kde zákazník nemá přístup.

- Tuto metodu použijte, pokud chcete zákazníkům umožnit zobrazení vybraných sešitů a playbooky, což jsou samostatné prostředky, které se můžou nacházet ve vlastní skupině prostředků.

I při udělení přístupu na úrovni skupiny prostředků budou mít zákazníci i nadále přístup k datům protokolu pro prostředky, ke kterým mají přístup, jako jsou protokoly z virtuálního počítače, a to i bez přístupu ke službě Azure Sentinel. Další informace najdete v tématu [Správa přístupu k datům Sentinel Azure podle prostředků](resource-context-rbac.md).

> [!TIP]
> Pokud potřebujete poskytnout svým zákazníkům přístup k celému předplatnému, můžete si prohlédnout pokyny v tématu [smlouvy Enterprise (EA)/průběžné platby (PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg).
>

### <a name="sample-azure-sentinel-csp-architecture"></a>Ukázka architektury CSP služby Azure Sentinel

Následující obrázek popisuje, jak můžou oprávnění popsaná v [předchozí části](#cloud-solutions-providers-csp) fungovat při poskytování přístupu ke zákazníkům CSP:

:::image type="content" source="media/mssp-protect-intellectual-property/csp-customers.png" alt-text="Chraňte duševní vlastnictví Azure Sentinel pomocí zákazníků CSP.":::

Na tomto obrázku:

- Uživatelé, kterým se **uděluje přístup k** předplatnému poskytovatele CSP, jsou uživatelé ve skupině agenti pro správu v klientovi Azure AD mssp.
- Další skupiny z MSSP získají přístup k prostředí zákazníka prostřednictvím Azure Lighthouse.
- Přístup zákazníků k prostředkům Azure spravuje služba Azure RBAC na úrovni skupiny prostředků.

    To umožňuje MSSPs skrýt podle potřeby součásti Sentinel Azure, jako jsou analytická pravidla a lovecké dotazy.

Další informace najdete také v dokumentaci ke [službě Azure Lighthouse](/azure/lighthouse/concepts/cloud-solution-provider).

## <a name="enterprise-agreements-ea--pay-as-you-go-payg"></a>Smlouvy Enterprise (EA)/průběžné platby (PAYG)

Pokud zákazník kupuje přímo od Microsoftu, má už plný přístup k prostředí Azure a nemůžete skrýt cokoli, co je v předplatném Azure zákazníka.

Místo toho v závislosti na typu prostředku, který chcete chránit, Ochraňte svoji duševní vlastnictví, které jste vyvinuli v Azure Sentinel, postupujte následovně:

### <a name="analytics-rules-and-hunting-queries"></a>Pravidla analýzy a lovecké dotazy

Pravidla analýzy a lovecké dotazy jsou obsaženy v rámci služby Azure Sentinel, a proto je nelze oddělit od pracovního prostoru Azure Sentinel.

I když má uživatel pouze oprávnění ke čtení Azure Sentinel, bude si moci dotaz zobrazit. V takovém případě doporučujeme hostovat vaše pravidla analýzy a lovecké dotazy ve vlastním tenantovi MSSP místo tenanta zákazníka.

K tomu budete potřebovat pracovní prostor ve vašem vlastním tenantovi s povoleným povolením Azure Sentinel a budete taky muset zobrazit pracovní prostor zákazníka prostřednictvím [Azure Lighthouse](multiple-tenants-service-providers.md).

Chcete-li vytvořit pravidlo analytika nebo lovecké dotazy v tenantovi MSSP, který odkazuje na data v tenantovi zákazníka, je nutné použít `workspace` příkaz následujícím způsobem:

```kql
workspace('<customer-workspace>').SecurityEvent
| where EventID == ‘4625’
```

Při přidávání `workspace` příkazu do pravidel analýzy Vezměte v úvahu následující skutečnosti:

- **V pracovním prostoru zákazníka nejsou žádné výstrahy**. Pravidla vytvořená tímto způsobem nevytvářejí v pracovním prostoru zákazníka upozornění ani incidenty. Výstrahy i incidenty budou existovat jenom v pracovním prostoru MSSP.

- **Vytvořte samostatné výstrahy pro každého zákazníka**. Když použijete tuto metodu, doporučujeme vám také, abyste pro každého zákazníka a zjišťování používali samostatná pravidla upozornění, protože příkaz pracovního prostoru se v každém případě liší.

    Můžete přidat jméno zákazníka k názvu pravidla výstrahy, abyste mohli snadno identifikovat zákazníka, na kterém se výstraha aktivuje. Samostatné výstrahy můžou mít velký počet pravidel, která budete možná chtít spravovat pomocí skriptování, nebo [Azure Sentinel jako kód](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928).

    Například:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-per-customer.png" alt-text="Vytvořte v pracovním prostoru MSSP samostatná pravidla pro každého zákazníka.":::

- **Vytvořte samostatné mssp pracovní prostory pro každého zákazníka**. Vytvoření samostatných pravidel pro každého zákazníka a zjišťování může mít za následek dosažení maximálního počtu analytických pravidel pro váš pracovní prostor (512). Pokud máte mnoho zákazníků a očekáváte, že dosáhnete tohoto limitu, můžete pro každého zákazníka vytvořit samostatný MSSP pracovní prostor.

    Například:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-and-workspace-per-customer.png" alt-text="Vytvořte v tenantovi MSSP pracovní prostor a pravidla pro každého zákazníka.":::

> [!IMPORTANT]
> Klíčem k úspěšnému použití této metody je automatizace pro správu velkého počtu pravidel napříč vašimi pracovními prostory.
>
> Další informace najdete v tématu [pravidla analýzy mezi jednotlivými pracovními prostory](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cross-workspace-analytics-rules/ba-p/1664211) .
>

### <a name="workbooks"></a>Workbooks

Pokud jste vytvořili sešit s Sentinelou v Azure, který nechcete, aby ho váš zákazník zkopíroval, zajistěte jeho hostování v tenantovi MSSP. Ujistěte se, že máte přístup k pracovním prostorům zákazníků prostřednictvím Azure Lighthouse, a pak nezapomeňte upravit sešit tak, aby používal tyto pracovní prostory zákazníka.

Například:

:::image type="content" source="media/mssp-protect-intellectual-property/cross-workspace-workbook.png" alt-text="Sešity mezi pracovními prostory":::

Další informace najdete v tématu [sešity mezi pracovními prostory](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks).

Pokud chcete, aby zákazník byl schopný zobrazit vizualizace sešitu a přitom zachovat tajný kód kódu, doporučujeme, abyste sešit exportovali do Power BI.

Export sešitu do Power BI:

- **Usnadňuje sdílení a vizualizaci sešitu**. Zákazníkovi můžete poslat odkaz na řídicí panel Power BI, kde si můžou zobrazit nahlášená data, aniž by to vyžadovalo přístup k Azure.
- **Povoluje plánování**. Nakonfigurujte Power BI k pravidelnému posílání e-mailů, které pro daný čas obsahují snímek řídicího panelu.

Další informace najdete v tématu [importování dat protokolu Azure monitor do Power BI](/azure/azure-monitor/visualize/powerbi).

### <a name="playbooks"></a>Playbooky

V závislosti na tom, kde se vytvořila analytická pravidla, která spouštějí PlayBook, můžete playbooky chránit takto:

- **Pravidla analýzy vytvořená v pracovním prostoru mssp**  Nezapomeňte vytvořit playbooky v tenantovi MSSP a získáte všechna data o incidentech a výstrahách z pracovního prostoru MSSP. Playbooky můžete připojit pokaždé, když ve svém pracovním prostoru vytvoříte nové pravidlo.

    Například:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-mssp-workspace.png" alt-text="Pravidla vytvořená v pracovním prostoru MSSP":::

- **Pravidla analýzy vytvořená v pracovním prostoru zákazníka** Pomocí Azure Lighthouse můžete připojit pravidla analýzy z pracovního prostoru zákazníka k PlayBook hostovanému v pracovním prostoru MSSP. V takovém případě PlayBook získá údaje o výstrahách a incidentech a všechny další informace o zákaznících z pracovního prostoru zákazník.

    Například:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-customer-workspace.png" alt-text="Pravidla vytvořená v pracovním prostoru zákazník":::

V obou případech, pokud PlayBook potřebuje přístup k prostředí Azure zákazníka, použijte uživatele nebo instanční objekt, který má tento přístup prostřednictvím Lighthouse.

Pokud ale PlayBook potřebuje přístup k prostředkům mimo Azure v tenantovi zákazníka, jako je například Azure AD, Office 365 nebo Microsoft 365 Defender, budete muset vytvořit instanční objekt s odpovídajícími oprávněními v tenantovi zákazníka a pak tuto identitu přidat do PlayBook.

> [!NOTE]
> Pokud používáte pravidla automatizace spolu s vaším playbooky, musíte nastavit oprávnění pravidla automatizace pro skupinu prostředků, kde playbooky Live.
> Další informace najdete v tématu [oprávnění pro pravidla automatizace ke spuštění playbooky](automate-incident-handling-with-automation-rules.md#permissions-for-automation-rules-to-run-playbooks).
>

## <a name="next-steps"></a>Další kroky

Další informace naleznete v tématu:

- [Azure Sentinel Technical PlayBook pro MSSPs](https://cloudpartners.transform.microsoft.com/download?assetname=assets/Azure-Sentinel-Technical-Playbook-for-MSSPs.pdf&download=1)
- [Správa více tenantů v Azure Sentinel jako MSSP](multiple-tenants-service-providers.md)
- [Rozšíření Azure Sentinelu napříč pracovními prostory a tenanty](extend-sentinel-across-workspaces-tenants.md)
- [Kurz: Vizualizace a monitorování dat](tutorial-monitor-your-data.md)
- [Kurz: nastavení automatických odpovědí na hrozby v Azure Sentinel](tutorial-respond-threats-playbook.md)
