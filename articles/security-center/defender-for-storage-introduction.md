---
title: Azure Defender pro úložiště – výhody a funkce
description: Seznamte se s výhodami a funkcemi Azure Defenderu pro úložiště.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 8979e315f188a5c21cce206c24f195f72096d438
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516518"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Úvod do Azure Defenderu pro úložiště


**Azure Defender pro úložiště** je vrstva zabezpečení Azure Native, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům úložiště nebo jejich zneužití. Využívá pokročilé možnosti zabezpečení AI a [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) k poskytování kontextových výstrah a doporučení zabezpečení.

Výstrahy zabezpečení se spouštějí při výskytu anomálií v aktivitě. Tyto výstrahy jsou integrovány do Azure Security Center a jsou také odesílány prostřednictvím e-mailu správcům předplatného s podrobnostmi o podezřelé aktivitě a doporučeních k tomu, jak tyto hrozby prozkoumat a opravit.


## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupná (GA)|
|Stanov|**Azure Defender pro úložiště** se fakturuje, jak je znázorněno na [stránce s cenami](security-center-pricing.md) .|
|Typy chráněných úložišť:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Soubory Azure](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|Cloud|![Ano](./media/icons/yes-icon.png) Komerční cloudy<br>![Ano](./media/icons/yes-icon.png) US Gov<br>![Ne](./media/icons/no-icon.png) Čína gov, jiné gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Jaký druh výstrah poskytuje Azure Defender pro úložiště?

Výstrahy zabezpečení se aktivují, když dojde k těmto akcím:

- **Podezřelé vzorce přístupu** – například úspěšný přístup z uzlu pro ukončení systému pro zápis nebo z IP adresy považované za podezřelé službou Microsoft Threat Intelligence
- **Podezřelé aktivity** – například extrakce dat neobvyklé nebo neobvyklé změny přístupových oprávnění
- **Nahrání škodlivého obsahu** , například potenciálních malwarových souborů (na základě analýzy reputace hodnot hash) nebo hostování obsahu podvodné pošty

Výstrahy obsahují podrobnosti o incidentu, který je aktivoval, a doporučení ohledně toho, jak tyto hrozby prozkoumat a opravit. Výstrahy se dají exportovat do Azure Sentinel nebo jakýchkoli jiných SIEM třetích stran nebo jiných externích nástrojů.

> [!TIP]
> Osvědčeným postupem je [nakonfigurovat Azure Defender pro úložiště](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center) na úrovni předplatného, ale můžete [ho nakonfigurovat i na jednotlivých účtech úložiště](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Co je analýza reputace hodnot hash pro malware?

Aby bylo možné zjistit, jestli je nahraný soubor podezřelý, Azure Defender pro úložiště používá analýzu reputace hodnoty hash, kterou podporuje [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). Nástroje ochrany před internetovými útoky neskenují nahrané soubory, ale prozkoumají protokoly úložiště a porovnávají hodnoty hash nově nahraných souborů o známých virech, trojských koním, spywaru a ransomwarem. 

Pokud je podezření, že se jedná o malware, Security Center zobrazí výstrahu a může volitelně poslat e-mail vlastníkovi úložiště k odstranění podezřelého souboru. Chcete-li nastavit automatické odebrání souborů, které analýza reputace hodnot hash indikuje jako malware, nasaďte [automatizaci pracovního postupu, která aktivuje výstrahy obsahující "potenciální malware nahraný do účtu úložiště"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Pokud chcete povolit možnosti ochrany před hrozbami Security Center, musíte v předplatném, které obsahuje příslušné úlohy, povolit Azure Defender.
>
> Službu **Azure Defender můžete povolit pro úložiště** buď na úrovni předplatného, nebo na úrovni prostředků.



## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o Azure Defenderu pro službu Storage.

Související materiály najdete v následujících článcích: 

- Bez ohledu na to, jestli je výstraha vygenerovaná Security Center nebo přijatá Security Center z jiného bezpečnostního produktu, můžete ji exportovat. Pokud chcete exportovat výstrahy do Azure Sentinel, všech SIEM třetích stran nebo jakýchkoli jiných externích nástrojů, postupujte podle pokynů v tématu [Export výstrah do Siem](continuous-export.md).
- [Jak povolit rozšířený Defender pro úložiště](../storage/common/azure-defender-storage-configure.md)
- [Seznam upozornění služby Azure Defender pro úložiště](alerts-reference.md#alerts-azurestorage)
- [Možnosti logiky hrozeb Microsoftu](https://go.microsoft.com/fwlink/?linkid=2128684)
