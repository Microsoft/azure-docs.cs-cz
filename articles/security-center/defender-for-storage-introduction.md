---
title: Azure Defender pro úložiště – výhody a funkce
description: Seznamte se s výhodami a funkcemi Azure Defenderu pro úložiště.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 8b6faa6e2e8928e008370cb0eca00595712a679d
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449017"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Seznámení se službou Azure Defender pro službu Storage

**Azure Defender pro úložiště** detekuje na vašich Azure Storage účtech potenciálně škodlivé aktivity. Data je možné chránit bez ohledu na to, jestli jsou uložená jako kontejnery objektů blob, sdílené složky nebo datová jezera.

Tato vrstva ochrany umožňuje řešit hrozby, *aniž* byste museli být odborníkem na zabezpečení a pomáhají spravovat systémy monitorování zabezpečení.


## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupná (GA)|
|Stanov|**Azure Defender pro úložiště** se fakturuje, jak je znázorněno na [stránce s cenami](security-center-pricing.md) .|
|Typy chráněných úložišť|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Soubory Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)<br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)|
|Cloud|![Ano](./media/icons/yes-icon.png) Komerční cloudy<br>![Ano](./media/icons/yes-icon.png) US Gov<br>![Ne](./media/icons/no-icon.png) Čína gov, jiné gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Jaký druh výstrah poskytuje Azure Defender pro úložiště?

Výstrahy zabezpečení se aktivují, když dojde k těmto akcím:

- **Podezřelá aktivita** – například účet úložiště byl úspěšně ZÍSKÁN z IP adresy, která se označuje jako aktivní uzel ukončení systému.
- **Neobvyklé chování** – například změny ve vzoru přístupu k účtu úložiště
- **Potenciální nahraný malware** – analýza reputace hodnoty hash indikuje, že nahraný soubor obsahuje malware.

Výstrahy obsahují podrobnosti o incidentu, který je aktivoval, a doporučení ohledně toho, jak tyto hrozby prozkoumat a opravit.

> [!TIP]
> Výstrahy úložiště můžete simulovat podle pokynů v [tomto blogovém příspěvku](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).


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
- [Jak povolit rozšířený Defender pro úložiště](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
- [Seznam upozornění služby Azure Defender pro úložiště](alerts-reference.md#alerts-azurestorage)
- [Možnosti logiky hrozeb Microsoftu](https://go.microsoft.com/fwlink/?linkid=2128684)