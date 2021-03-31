---
title: Správa žádosti o podporu Azure
description: Popisuje, jak zobrazit žádosti o podporu, odesílat zprávy, měnit úroveň závažnosti žádosti, sdílet diagnostické informace s podporou Azure, znovu otevřít uzavřenou žádost o podporu a nahrát soubory.
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 4d0c03e0035f6b71a23891ac1691f5421c1bdb76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102502514"
---
# <a name="manage-an-azure-support-request"></a>Správa žádosti o podporu Azure

Po [Vytvoření žádosti o podporu Azure](how-to-create-azure-support-request.md)ji můžete spravovat v [Azure Portal](https://portal.azure.com), který je popsaný v tomto článku. Žádosti můžete také vytvářet a spravovat programově, pomocí [lístku podpory Azure REST API](/rest/api/support)nebo pomocí [Azure CLI](/cli/azure/azure-cli-support-request).

## <a name="view-support-requests"></a>Zobrazení žádostí o podporu

Podrobnosti a stav žádostí o podporu si můžete zobrazit tak, že přejdete na **help + podpora**  >   **všech žádostí o podporu**.

:::image type="content" source="media/how-to-manage-azure-support-request/all-requests-lower.png" alt-text="Všechny žádosti o podporu":::

Na této stránce můžete vyhledat, filtrovat a seřadit žádosti o podporu. Vyberte žádost o podporu pro zobrazení podrobností, včetně její závažnosti a všech zpráv přidružených k žádosti.

## <a name="send-a-message"></a>Odeslat zprávu

1. Na stránce **všechny žádosti o podporu** vyberte žádost o podporu.

1. Na stránce **žádost o podporu** vyberte možnost **Nová zpráva**.

1. Zadejte zprávu a vyberte **Odeslat**.

## <a name="change-the-severity-level"></a>Změna úrovně závažnosti

> [!NOTE]
> Maximální úroveň závažnosti závisí na [plánu podpory](https://azure.microsoft.com/support/plans).
>

1. Na stránce **všechny žádosti o podporu** vyberte žádost o podporu.

1. Na stránce **žádost o podporu** vyberte **změnit**.

    :::image type="content" source="media/how-to-manage-azure-support-request/change-severity.png" alt-text="Změna závažnosti žádosti o podporu":::

1. Azure Portal zobrazuje jednu ze dvou obrazovek v závislosti na tom, jestli je vaše žádost už přiřazená pracovníkovi podpory:

    - Pokud se vaše žádost nepřiřadila, zobrazí se obrazovka podobná této. Vyberte novou úroveň závažnosti a pak vyberte **změnit**.

        :::image type="content" source="media/how-to-manage-azure-support-request/unassigned-can-change-severity.png" alt-text="Vybrat novou úroveň závažnosti":::

    - Pokud se vaše žádost přiřadila, zobrazí se obrazovka podobná této. Vyberte **OK** a pak vytvořte [novou zprávu](#send-a-message) pro vyžádání změny úrovně závažnosti.

        :::image type="content" source="media/how-to-manage-azure-support-request/assigned-cant-change-severity.png" alt-text="Nejde vybrat novou úroveň závažnosti.":::

## <a name="share-diagnostic-information-with-azure-support"></a>Sdílet diagnostické informace s podporou Azure

Když vytvoříte žádost o podporu, ve výchozím nastavení je vybraná možnost **Sdílet diagnostické informace** . Díky tomu může podpora Azure shromažďovat [diagnostické informace](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) z vašich prostředků Azure:

* Po vytvoření žádosti tuto možnost nemůžete vymazat.

* Pokud jste při vytváření žádosti tuto možnost vymazali, můžete ji po vytvoření žádosti vybrat.

    1. Na stránce **všechny žádosti o podporu** vyberte žádost o podporu.
    
    1. Na stránce **žádost o podporu** vyberte **udělit oprávnění** a pak vyberte **Ano** a **OK**.
    
        :::image type="content" source="media/how-to-manage-azure-support-request/grant-permission-manage.png" alt-text="Udělení oprávnění pro diagnostické informace":::

## <a name="upload-files"></a>Nahrání souborů

Možnost nahrávání souborů můžete použít k nahrání diagnostických souborů nebo jakýchkoli dalších souborů, které považujete za relevantní pro žádost o podporu.

1. Na stránce **všechny žádosti o podporu** vyberte žádost o podporu.

1. Na stránce **žádost o podporu** vyhledejte soubor a pak vyberte **Odeslat**. Pokud máte více souborů, opakujte tento postup.

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="Nahrát soubor":::

### <a name="file-upload-guidelines"></a>Pokyny pro nahrávání souborů

Při použití možnosti nahrávání souborů postupujte podle těchto pokynů:

* K ochraně vašich osobních údajů nezahrnujte do nahrávání žádné osobní údaje.
* Název souboru nesmí být delší než 110 znaků.
* Nemůžete nahrát víc než jeden soubor.
* Soubory nemohou být větší než 4 MB.
* Všechny soubory musí mít příponu názvu souboru, například *. docx* nebo *. xlsx*. Následující tabulka uvádí přípony názvů souborů, které jsou povoleny pro nahrání.

| 0-9, A-C    | D – G   | H-N         | O-Q   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | . dat  | . har        | . ODX  | . rar     | .uccapilog | .xlam   |
| . a          | . DB   | .hwl        | . oft  | . RDL     | .uccplog   | .xlr    |
| . ABC        | . DMP  | . ICS        | . old  | . rdlc    | .udcx      | .xls    |
| . adm        | .do_  | . ini        | . One  | .re_     | .vb_       | .xlsb   |
| . aspx       | .doc  | .java       | . OSD  | . Remove  | .vbs_      | .xlsm   |
| . ATF        | .docm | .jpg        | . MIMO  | . Ren     | . vcf       | .xlsx   |
| . b          | .docx | . LDF        | . P1   | . rename  | . vsd       | .xlt    |
| .ba_        | .dotm | . hlavičkový formát | .pcap | .rft     | . wdb       | .xltx   |
| . bak        | .dotx | .lo_        | soubor. pdb  | . rpt     | . WKS       | .xml    |
| . blg        | .dtsx | . log        | .pdf  | .rte     | . WMA       | . XMLA   |
| .CA_        | . EDS  | .lpk        | .piz  | .rtf     | . wmv       | .xps    |
| . SOUBORŮ        | . EMF  | . manifest   | .pmls | . Run     | . WMZ       | . xsd    |
| . Cap        | . eml  | . Master     | .png  | .saz     | . WPS       | . xsn    |
| .catx       | .emz  | . mdmp       | .potx | SQL     | .wpt       | . xxx    |
| . Priorita        | . err  | . mof        | .ppt  | .sqlplan | . WSDL      | .z_     |
| . komprimovaná | . ETL  | . mp3        | .pptm | . STP     | . wsp       | .z01    |
| . Konfigurace     | . evt  | . mpg        | .pptx | .svclog  | .wtl       | .z02    |
| .cpk        | .evtx | .ms_        | . prn  | .tdb     | -          | . Zi     |
| . cpp        | . DODATEČNÉ   | . msg        | . PSV  | .tdf     | -          | .zi_    |
| .cs         | .ex_  | soubor. mso        | PST  | . text    | -          | .zip    |
| . Formát        | .ex0  | . msu        | . pub  | .thmx    | -          | .zip_   |
| .cvr        | . FRD  | . nfo        | -     | .tif     | -          | .zipp   |
| -           | .gif  | -           | -     | . trc     | -          | . zip |
| -           | identifikátor. GUID | -           | -     | . TTD     | -          | .zippy  |
| -           | . gz   | -           | -     | .tx_     | -          | .zipx   |
| -           | -     | -           | -     | .txt     | -          | .zit    |
| -           | -     | -           | -     | -        | -          | .zix    |
| -           | -     | -           | -     | -        | -          | . zzz    |

## <a name="close-a-support-request"></a>Zavřít žádost o podporu

Pokud potřebujete zavřít žádost o podporu, [odešlete zprávu s](#send-a-message) žádostí o uzavření žádosti.

## <a name="reopen-a-closed-request"></a>Znovu otevřít uzavřenou žádost

Pokud potřebujete znovu otevřít uzavřenou žádost o podporu, vytvořte [novou zprávu](#send-a-message), která automaticky znovu otevře požadavek.

## <a name="cancel-a-support-plan"></a>Zrušení plánu podpory

Pokud potřebujete zrušit plán podpory, přečtěte si článek [zrušení plánu podpory](../../cost-management-billing/manage/cancel-azure-subscription.md#cancel-a-support-plan).

## <a name="next-steps"></a>Další kroky

[Jak vytvořit žádost o podporu Azure](how-to-create-azure-support-request.md)

[Rozhraní REST API lístku podpory Azure](/rest/api/support)
