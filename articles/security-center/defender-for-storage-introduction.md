---
title: Azure Defender pro úložiště – výhody a funkce
description: Seznamte se s výhodami a funkcemi Azure Defenderu pro úložiště.
author: memildin
ms.author: memildin
ms.date: 02/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: eb1635cec2b0bcf7f2c13101b2aeab25a869dc66
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558567"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Úvod do Azure Defenderu pro úložiště

**Azure Defender pro úložiště** je vrstva zabezpečení Azure Native, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům úložiště nebo jejich zneužití. Využívá pokročilé možnosti zabezpečení AI a [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) k poskytování kontextových výstrah a doporučení zabezpečení.

Výstrahy zabezpečení se spouštějí při výskytu anomálií v aktivitě. Tyto výstrahy jsou integrovány do Azure Security Center a jsou také odesílány prostřednictvím e-mailu správcům předplatného s podrobnostmi o podezřelé aktivitě a doporučeních k tomu, jak tyto hrozby prozkoumat a opravit.

## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Obecná dostupnost (GA)|
|Stanov|**Azure Defender pro úložiště** se fakturuje, jak je znázorněno na [stránce s cenami](security-center-pricing.md) .|
|Typy chráněných úložišť:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Čína gov, jiné gov|
|||


## <a name="what-are-the-benefits-of-azure-defender-for-storage"></a>Jaké jsou výhody Azure Defenderu pro úložiště?

Azure Defender pro úložiště poskytuje:

- **Azure – nativní zabezpečení** – s jedním kliknutím na povolit, Defender pro úložiště chrání data uložená v Azure Blob, Azure Files a data laků. Služba Defender pro úložiště jako Azure-Native Service poskytuje centralizované zabezpečení napříč všemi datovými assety, které spravuje Azure a jsou integrované s dalšími službami zabezpečení Azure, jako je Azure Sentinel.
- **Bohatá detekční sada** – využívá technologii Microsoft Threat Intelligence, detekce v programu Defender pro úložiště pokrývá horní hrozby úložiště, jako je anonymní přístup, zneužití přihlašovacích údajů, sociální inženýrství, zneužití oprávnění a škodlivý obsah.
- **Reakce na** služby pro automatizaci s Security Center škálováním usnadňuje prevenci zjištěným hrozbám a reagovat na ně. Přečtěte si další informace v [automatizaci odpovědí na aktivační události Security Center](workflow-automation.md).

:::image type="content" source="media/defender-for-storage-introduction/defender-for-storage-high-level-overview.png" alt-text="Přehled funkcí služby Azure Defender pro úložiště na nejvyšší úrovni":::


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Jaký druh výstrah poskytuje Azure Defender pro úložiště?

Výstrahy zabezpečení se aktivují, když dojde k těmto akcím:

- **Podezřelé vzorce přístupu** – například úspěšný přístup z uzlu pro ukončení systému pro zápis nebo z IP adresy považované za podezřelé službou Microsoft Threat Intelligence
- **Podezřelé aktivity** – například extrakce dat neobvyklé nebo neobvyklé změny přístupových oprávnění
- **Nahrání škodlivého obsahu** , například potenciálních malwarových souborů (na základě analýzy reputace hodnot hash) nebo hostování obsahu podvodné pošty

Výstrahy obsahují podrobnosti o incidentu, který je aktivoval, a doporučení ohledně toho, jak tyto hrozby prozkoumat a opravit. Výstrahy se dají exportovat do Azure Sentinel nebo jakýchkoli jiných SIEM třetích stran nebo jiných externích nástrojů.

> [!TIP]
> Osvědčeným postupem je [nakonfigurovat Azure Defender pro úložiště](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center) na úrovni předplatného, ale můžete [ho nakonfigurovat i na jednotlivých účtech úložiště](../storage/common/azure-defender-storage-configure.md?tabs=azure-portal).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Co je analýza reputace hodnot hash pro malware?

Aby bylo možné zjistit, jestli je nahraný soubor podezřelý, Azure Defender pro úložiště používá analýzu reputace hodnoty hash, kterou podporuje [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). Nástroje ochrany před internetovými útoky neskenují nahrané soubory, ale prozkoumají protokoly úložiště a porovnávají hodnoty hash nově nahraných souborů o známých virech, trojských koním, spywaru a ransomwarem. 

Pokud je podezření, že se jedná o malware, Security Center zobrazí výstrahu a může volitelně poslat e-mail vlastníkovi úložiště k odstranění podezřelého souboru. Chcete-li nastavit automatické odebrání souborů, které analýza reputace hodnot hash indikuje jako malware, nasaďte [automatizaci pracovního postupu, která aktivuje výstrahy obsahující "potenciální malware nahraný do účtu úložiště"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Pokud chcete povolit možnosti ochrany před hrozbami Security Center, musíte v předplatném, které obsahuje příslušné úlohy, povolit Azure Defender.
>
> Službu **Azure Defender můžete povolit pro úložiště** buď na úrovni předplatného, nebo na úrovni prostředků.

## <a name="trigger-a-test-alert-for-azure-defender-for-storage"></a>Aktivovat upozornění testu pro Azure Defender pro úložiště

Pokud chcete otestovat výstrahy zabezpečení z Azure Defenderu pro úložiště ve vašem prostředí, vygenerujte výstrahu "přístup z uzlu pro opuštění příkazu k účtu úložiště" pomocí následujících kroků:

1. Otevřete účet úložiště s povoleným službou Azure Defender pro úložiště.
1. Z bočního panelu vyberte kontejnery a otevřete existující kontejner nebo vytvořte nový.

    :::image type="content" source="media/defender-for-storage-introduction/opening-storage-container.png" alt-text="Otevření kontejneru objektů BLOB z Azure Storage účtu" lightbox="media/defender-for-storage-introduction/opening-storage-container.png":::

1. Nahrajte do tohoto kontejneru nějaký soubor.

    > [!CAUTION]
    > Nenahrávat soubor obsahující citlivá data.

1. Pomocí místní nabídky nahraného souboru vyberte "generovat SAS".

    :::image type="content" source="media/defender-for-storage-introduction/generate-sas.png" alt-text="Možnost generovat SAS pro soubor v kontejneru objektů BLOB":::

1. Ponechte výchozí možnosti a vyberte **Generovat token SAS a adresu URL**.

1. Zkopírujte vygenerovanou adresu URL SAS.

1. V místním počítači otevřete prohlížeč pro mandát.

    > [!TIP]
    > Můžete si stáhnout ze serveru projektu služby [https://www.torproject.org/download/](https://www.torproject.org/download/) .

1. V prohlížeči mandát přejděte na adresu URL SAS.

1. Stáhněte si soubor, který jste nahráli v kroku 3.

    Během dvou hodin obdržíte následující výstrahu zabezpečení z Security Center:

    :::image type="content" source="media/defender-for-storage-introduction/tor-access-alert-storage.png" alt-text="Výstraha zabezpečení týkající se přístupu z uzlu pro ukončení operace":::

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o Azure Defenderu pro službu Storage.

Související materiály najdete v následujících článcích: 

- Bez ohledu na to, jestli je výstraha vygenerovaná Security Center nebo přijatá Security Center z jiného bezpečnostního produktu, můžete ji exportovat. Pokud chcete exportovat výstrahy do Azure Sentinel, všech SIEM třetích stran nebo jakýchkoli jiných externích nástrojů, postupujte podle pokynů v tématu [Export výstrah do Siem](continuous-export.md).
- [Jak povolit rozšířený Defender pro úložiště](../storage/common/azure-defender-storage-configure.md)
- [Seznam upozornění služby Azure Defender pro úložiště](alerts-reference.md#alerts-azurestorage)
- [Možnosti logiky hrozeb Microsoftu](https://go.microsoft.com/fwlink/?linkid=2128684)