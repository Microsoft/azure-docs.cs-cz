---
title: Podpora zdrojové mapy pro javascriptové aplikace – Azure Monitor Application Insights
description: Přečtěte si, jak nahrát zdrojové mapy do vlastního kontejneru objektů blob účtu úložiště pomocí Application Insights.
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 03/04/2020
ms.openlocfilehash: 4b452b31338760a8f53eed54420319101836bc00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474879"
---
# <a name="source-map-support-for-javascript-applications"></a>Podpora zdrojové mapy pro javascriptové aplikace

Application Insights podporuje nahrávání zdrojových map do vlastního kontejneru objektů blob účtu úložiště.
Zdrojové mapy lze použít k unminify zásobníky volání nastránce podrobnosti o transakci od konce do konce. Jakákoli výjimka odeslaná sadou [JavaScript SDK][ApplicationInsights-JS] nebo [sadou Node.js SDK][ApplicationInsights-Node.js] může být nezahlcena zdrojovými mapami.

![Zrušení zásobníku volání propojením s účtem úložiště](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>Vytvoření nového účtu úložiště a kontejneru objektů Blob

Pokud už máte existující účet úložiště nebo kontejner objektů blob, můžete tento krok přeskočit.

1. [Vytvoření nového účtu úložiště][create storage account]
2. [Vytvořte kontejner objektů blob][create blob container] uvnitř účtu úložiště. Nezapomeňte nastavit "Úroveň veřejného přístupu" na , abyste `Private`zajistili, že zdrojové mapy nebudou veřejně přístupné.

> [!div class="mx-imgBorder"]
>![Úroveň přístupu ke kontejneru musí být nastavena na soukromou.](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>Přepnutí zdrojových map do kontejneru objektů Blob

Kanál průběžného nasazení byste měli integrovat s účtem úložiště tak, že ho nakonfigurujete tak, aby automaticky nahrál zdrojové mapy do nakonfigurovaného kontejneru objektů Blob. Neměli byste nahrát zdrojové mapy do podsložky v kontejneru objektů Blob; v současné době bude zdrojová mapa načtena pouze z kořenové složky.

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Nahrání zdrojových map přes Azure Pipelines (doporučeno)

Pokud používáte Azure Pipelines k průběžnému vytváření a nasazování aplikace, přidejte do kanálu [úlohu Azure File Copy,][azure file copy] abyste automaticky nahráli zdrojové mapy.

> [!div class="mx-imgBorder"]
> ![Přidání úlohy Azure File Copy do kanálu pro nahrání zdrojových map do úložiště objektů blob Azure](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>Konfigurace prostředku Application Insights pomocí účtu úložiště zdrojové mapy

### <a name="from-the-end-to-end-transaction-details-page"></a>Ze stránky podrobností o transakcích začátku do konce

Na kartě podrobnosti o transakcích začátku do konce můžete kliknout na *Unminify* a zobrazí se výzva ke konfiguraci, zda je prostředek nenakonfigurován.

1. Na portálu zobrazte podrobnosti o výjimce, která je minifikovaná.
2. Klikněte na *Unminify*
3. Pokud prostředek nebyl nakonfigurován, zobrazí se zpráva s výzvou ke konfiguraci.

### <a name="from-the-properties-page"></a>Ze stránky vlastností

Pokud chcete nakonfigurovat nebo změnit účet úložiště nebo kontejner objektů Blob, který je propojený s vaším prostředkem Application Insights, můžete to udělat zobrazením karty *Vlastnosti* prostředku Application Insights.

1. Přejděte na kartu *Vlastnosti* prostředku Application Insights.
2. Klikněte na *Změnit zdrojovou mapu kontejneru objektů blob*.
3. Vyberte jiný kontejner objektů Blob jako zdrojový kontejner map.
4. Klikněte na `Apply` (Další).

> [!div class="mx-imgBorder"]
> ![Změna konfigurace vybraného kontejneru objektů blob Azure přechodem do okna Vlastnosti](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>Řešení potíží

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>Požadované nastavení řízení přístupu na základě rolí (RBAC) v kontejneru objektů Blob

Každý uživatel na portálu, který používá tuto funkci, musí být přiřazen alespoň jako [čtečka dat objektů blob úložiště][storage blob data reader] do kontejneru objektů blob. Tuto roli je nutné přiřadit komukoli jinému, kdo bude prostřednictvím této funkce používat zdrojové mapy.

> [!NOTE]
> V závislosti na tom, jak byl kontejner vytvořen, to nemusí být automaticky přiřazeny k vám nebo vašemu týmu.

### <a name="source-map-not-found"></a>Zdrojová mapa nebyla nalezena.

1. Ověřte, zda je odpovídající zdrojová mapa odeslána do správného kontejneru objektů blob
2. Ověřte, zda je zdrojový mapový soubor pojmenován po souboru `.map`JavaScript, na který mapuje, s upevněným .
    - Bude například `/static/js/main.4e2ca5fa.chunk.js` hledat objekt blob s názvem`main.4e2ca5fa.chunk.js.map`
3. Zkontrolujte konzolu prohlížeče a zjistěte, zda nejsou protokolovány nějaké chyby. Zahrňte to do libovolné houstnou.

## <a name="next-steps"></a>Další kroky

* [Úloha kopírování souborů Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal
[storage blob data reader]: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme