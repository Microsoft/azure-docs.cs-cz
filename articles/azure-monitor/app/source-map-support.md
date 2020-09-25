---
title: Podpora zdrojového mapování pro aplikace JavaScriptu – Azure Monitor Application Insights
description: Naučte se nahrávat zdrojová mapování do vlastního kontejneru objektů BLOB v účtu úložiště pomocí Application Insights.
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 5b15492a36032a53ac81929eb55bce0bc70e040c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335126"
---
# <a name="source-map-support-for-javascript-applications"></a>Podpora zdrojového mapování pro aplikace JavaScriptu

Application Insights podporuje nahrávání zdrojových map do vlastního kontejneru objektů BLOB v účtu úložiště.
Zdrojové mapy lze použít k zrušení minifikace zásobníků volání, které byly nalezeny na stránce Podrobnosti o koncové transakci. Jakoukoli výjimku odeslanou sadou [JavaScript SDK][ApplicationInsights-JS] nebo sadou [Node.js SDK][ApplicationInsights-Node.js] lze unminified se zdrojovými mapami.

![Zrušení minifikace zásobníku volání propojením s účtem úložiště](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>Vytvoření nového účtu úložiště a kontejneru objektů BLOB

Pokud již máte existující účet úložiště nebo kontejner objektů blob, můžete tento krok přeskočit.

1. [Vytvořit nový účet úložiště][create storage account]
2. [Vytvořte kontejner objektů BLOB][create blob container] v účtu úložiště. Nezapomeňte nastavit úroveň veřejného přístupu na `Private` , abyste zajistili, že vaše zdrojová mapování nejsou veřejně přístupná.

> [!div class="mx-imgBorder"]
>![Úroveň přístupu ke kontejneru musí být nastavená na Private.](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>Vložení zdrojových map do kontejneru objektů BLOB

Svůj kanál průběžného nasazování byste měli integrovat do svého účtu úložiště tak, že ho nakonfigurujete tak, aby automaticky odesílal vaše zdrojové mapy do nakonfigurovaného kontejneru objektů BLOB

Zdrojové mapy je možné odeslat do kontejneru Blob Storage se stejnou strukturou složek, kterou byly zkompilovány & nasazeny pomocí nástroje. Běžným případem použití je vytvoření předpony složky pro nasazení s jeho verzí, např. `1.2.3/static/js/main.js` . Při unminifying prostřednictvím kontejneru objektů blob Azure s názvem se `sourcemaps` pokusí načíst zdrojové mapování umístěné na adrese `sourcemaps/1.2.3/static/js/main.js.map` .

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Nahrát zdrojová mapování přes Azure Pipelines (doporučeno)

Pokud používáte Azure Pipelines pro průběžné sestavování a nasazování vaší aplikace, přidejte do svého kanálu úlohu [Azure File Copy][azure file copy] a automaticky tak nahrajte Vaše zdrojová mapování.

> [!div class="mx-imgBorder"]
> ![Přidejte úlohu kopírování souborů Azure do vašeho kanálu, abyste nahráli vaše zdrojová mapování do Azure Blob Storage](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>Konfigurace prostředku Application Insights pomocí účtu úložiště zdrojového mapování

### <a name="from-the-end-to-end-transaction-details-page"></a>Na stránce s podrobnostmi o transakci od začátku až do konce

Na kartě Podrobnosti transakce na konci můžete kliknout na *zrušení minifikace* a zobrazí se výzva ke konfiguraci, jestli je prostředek nenakonfigurovaný.

1. Na portálu se zobrazí podrobnosti o výjimce, která je minifikovaného.
2. Klikněte na *zrušení minifikace*
3. Pokud váš prostředek není nakonfigurovaný, zobrazí se zpráva s výzvou, abyste ji nakonfigurovali.

### <a name="from-the-properties-page"></a>Ze stránky Properties (vlastnosti)

Pokud chcete nakonfigurovat nebo změnit účet úložiště nebo kontejner objektů blob, který je propojený s vaším prostředkem Application Insights, můžete to udělat zobrazením karty *vlastnosti* prostředku Application Insights.

1. Přejděte na kartu *vlastnosti* prostředku Application Insights.
2. Klikněte na *změnit kontejner objektů BLOB zdrojového mapování*.
3. Jako kontejner zdrojového mapování vyberte jiný kontejner objektů BLOB.
4. Klikněte na `Apply` (Další).

> [!div class="mx-imgBorder"]
> ![Překonfigurujte vybraný kontejner Azure Blob tak, že přejdete na okno Vlastnosti.](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>Řešení potíží

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>Požadované nastavení řízení přístupu na základě role (RBAC) na kontejneru objektů BLOB

Každý uživatel na portálu, který tuto funkci používá, musí být aspoň přiřazený jako [čtečka dat objektů BLOB úložiště][storage blob data reader] do kontejneru objektů BLOB. Tuto roli musíte přiřadit někomu jinému, kdo bude používat zdrojové mapy prostřednictvím této funkce.

> [!NOTE]
> V závislosti na tom, jak byl kontejner vytvořen, to nemusí být automaticky přiřazeno vám nebo vašemu týmu.

### <a name="source-map-not-found"></a>Mapování zdrojového kódu se nenašlo.

1. Ověřte, jestli se odpovídající zdrojová mapa nahrála do správného kontejneru objektů BLOB.
2. Ověřte, zda je zdrojový soubor mapování pojmenován po souboru JavaScriptu, na který je namapován, s příponou `.map` .
    - Vyhledá třeba `/static/js/main.4e2ca5fa.chunk.js` objekt BLOB s názvem `main.4e2ca5fa.chunk.js.map`
3. Zkontrolujte konzolu prohlížeče a zjistěte, jestli se nějaké chyby protokolují. Zahrňte do libovolného lístku podpory.

## <a name="next-steps"></a>Další kroky

* [Úloha kopírování souborů Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: ../../storage/common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: ../../storage/blobs/storage-quickstart-blobs-portal.md
[storage blob data reader]: ../../role-based-access-control/built-in-roles.md#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme
