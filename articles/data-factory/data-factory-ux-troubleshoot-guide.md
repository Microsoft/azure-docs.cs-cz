---
title: Řešení potíží s Azure Data Factory UX
description: Naučte se řešit potíže s Azure Data Factorym UX.
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: b423b008453ca5f1b76e5c277f5a434dbf14cdb1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382833"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Řešení potíží s Azure Data Factorym UX

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje běžné metody řešení potíží pro Azure Data Factory UX.

## <a name="adf-ux-not-loading"></a>Nenačítá se uživatelské prostředí ADF

> [!NOTE]
> Azure Data Factory UX oficiálně podporuje Microsoft Edge a Google Chrome. Používání jiných webových prohlížečů může vést k neočekávanému nebo nedokumentovanýmu chování.

### <a name="third-party-cookies-blocked"></a>Blokované soubory cookie třetích stran

UX v uživatelském rozhraní ADF používá soubory cookie prohlížeče k zachování uživatelské relace a umožňuje interaktivní vývojové a monitorovací prostředí. Je možné, že prohlížeč blokuje soubory cookie třetích stran, protože používáte relaci anonymním nebo máte povolený blokování služby Active Directory. Blokování souborů cookie třetích stran může při načítání portálu způsobit problémy, jako je například přesměrování na prázdnou stránku, https://adf.azure.com/accesstoken.html nebo získání zprávy s upozorněním, že soubory cookie třetích stran jsou zablokovány. Pokud chcete tento problém vyřešit, povolte v prohlížeči možnosti souborů cookie třetích stran pomocí následujících kroků:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Povolení všech souborů cookie

1. Navštivte **Chrome://Settings/cookies** v prohlížeči.
1. Vyberte možnost **povolení všech souborů cookie** . 

    ![Povolení všech souborů cookie v Chrome](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Obnovte uživatelské prostředí ADF a zkuste to znovu.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Umožněte použití souborů cookie jenom v uživatelském prostředí ADF.
Pokud nechcete povolit všechny soubory cookie, můžete volitelně povolit jenom uživatelské prostředí ADF:
1. Navštivte **Chrome://Settings/cookies**.
1. Vyberte možnost **Přidat** do **webů, které můžou vždycky používat soubory cookie** . 

    ![Přidání uživatelského prostředí ADF do povolených webů v Chrome](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Přidejte web **ADF.Azure.com** , vyhledejte možnost **všechny soubory cookie** a uložte. 

    ![Povolení všech souborů cookie z webu ADF UX](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Obnovte uživatelské prostředí ADF a zkuste to znovu.

### <a name="microsoft-edge"></a>Microsoft Edge

1. Navštivte **Edge://Settings/Content/cookies** v prohlížeči.
1. Ujistěte se, že je povolená možnost **Povolit webům ukládat a číst data souborů cookie** a jestli je možnost **Blokovat soubory cookie třetích stran** zakázaná. 

    ![Povolení všech souborů cookie v Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Obnovte uživatelské prostředí ADF a zkuste to znovu.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Umožněte použití souborů cookie jenom v uživatelském prostředí ADF.

Pokud nechcete povolit všechny soubory cookie, můžete volitelně povolit jenom uživatelské prostředí ADF:

1. Navštivte **Edge://Settings/Content/cookies**.
1. V části **povoleno** vyberte **Přidat** a přidat web **ADF.Azure.com** . 

    ![Přidání uživatelského prostředí ADF do povolených lokalit na hraničních zařízeních](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Obnovte uživatelské prostředí ADF a zkuste to znovu.

## <a name="connection-failed-on-adf-ux"></a>Nepovedlo se připojit k UŽIVATELSKÉmu prostředí ADF

Někdy se vám v uživatelském prostředí ADF po kliknutí na **test Connection**, **Preview** atd. zobrazí chyby "připojení nebylo úspěšné".

![Připojení selhalo.](media/data-factory-ux-troubleshoot-guide/connection-failed.png)

V takovém případě můžete v prohlížeči nejprve vyzkoušet stejnou operaci s režimem procházení InPrivate.

Pokud to pořád nefunguje, otevřete v prohlížeči stisknutím klávesy F12 **vývojářské nástroje**. Vraťte se na kartu **síť** , vyhledejte možnost **Zakázat mezipaměť**, opakujte neúspěšnou operaci a najděte neúspěšnou žádost (červeně).

![Neúspěšná žádost](media/data-factory-ux-troubleshoot-guide/failed-request.png)

Pak vyhledejte **název hostitele** (v tomto případě **dpnortheurope.svc.DataFactory.Azure.com**) z **adresy URL požadavku** neúspěšného požadavku.

Zadejte **název hostitele** přímo do adresního řádku prohlížeče. Pokud se v prohlížeči zobrazí 404, obvykle to znamená, že je na straně klienta ok a problém je na straně služby ADF. Zasouborte lístek podpory s **ID aktivity** z chybové zprávy uživatelského rozhraní ADF.

![Prostředek se nenašel.](media/data-factory-ux-troubleshoot-guide/status-code-404.png)

Pokud ne nebo se v prohlížeči zobrazí podobná chyba, obvykle to znamená, že došlo k nějakému problému na straně klienta. Dále postupujte podle kroků pro řešení potíží.

![Chyba na straně klienta](media/data-factory-ux-troubleshoot-guide/client-side-error.png)

Otevřete **příkazový řádek** a zadejte příkaz **nslookup dpnortheurope.svc.DataFactory.Azure.com**. Normální odpověď by měla vypadat nějak takto:

![Odezva příkazu 1](media/data-factory-ux-troubleshoot-guide/command-response-1.png)

Pokud se zobrazí normální odpověď DNS, požádejte o pomoc místní podporu IT, aby zkontrolovala nastavení brány firewall na základě toho, jestli je připojení HTTPS k tomuto názvu hostitele blokované nebo ne. Pokud se problém nepovedlo vyřešit, zapište do souboru lístek podpory s **ID aktivity** z chybové zprávy uživatelského rozhraní ADF.

Pokud se vám zobrazuje něco jiného, obvykle to znamená, že při překladu názvu DNS dojde k nějakému problému se serverem DNS. To může být obvykle změnou poskytovatele internetových služeb (Internet Service Provider) nebo DNS (například na Google DNS 8.8.8.8). Pokud se problém opakuje, můžete si dále vyzkoušet příkaz **nslookup DataFactory.Azure.com** a **nslookup Azure.com** , abyste viděli, na jaké úrovni se překlad DNS nezdařil, a odeslat všechny informace do místní podpory IT nebo poskytovatele internetových služeb pro řešení potíží. Pokud se domníváte, že problém je pořád na straně Microsoftu, zapište lístek podpory s **ID aktivity** z chybové zprávy uživatelského rozhraní ADF.

![Odezva příkazu 2](media/data-factory-ux-troubleshoot-guide/command-response-2.png)

## <a name="change-linked-service-type-in-datasets"></a>Změna typu propojené služby v datových sadách

Datová sada formátu souboru se dá použít se všemi konektory založenými na souborech, například můžete nakonfigurovat datovou sadu Parquet v Azure Blob nebo Azure Data Lake Storage Gen2. Všimněte si, že každý konektor podporuje v aktivitě jinou sadu nastavení souvisejících s úložištěm dat a s jiným aplikačním modelem. 

Pokud v uživatelském rozhraní pro vytváření ADF používáte v aktivitě datovou sadu formátu souborů, včetně kopírování, vyhledávání, GetMetadata, odstranění aktivit a v datové sadě, kterou chcete nasměrovat na propojenou službu jiného typu z aktuálního (např. přepnutí ze systému souborů na ADLS Gen2), zobrazí se následující zpráva upozornění. Aby se zajistilo, že se jedná o čistý přepínač, jsou kanály a aktivity, které odkazují na tuto datovou sadu, upraveny tak, aby používaly nový typ a všechna existující nastavení úložiště dat, která jsou nekompatibilní s novým typem, se vymažou, protože už nebude platit.

Pokud chcete získat další informace o podporovaných nastaveních úložiště dat pro jednotlivé konektory, můžete přejít na příslušný článek o konektoru – > vlastnosti aktivity kopírování, abyste viděli seznam podrobných vlastností. Přečtěte si téma [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md).

![Zpráva upozornění](media/data-factory-ux-troubleshoot-guide/warning-message.png)

## <a name="next-steps"></a>Další kroky

Pro další nápovědu k řešení potíží zkuste tyto prostředky:

* [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
* [Stack Overflow fórum pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
* [Videa Azure](https://azure.microsoft.com/resources/videos/index/)
* [Stránka s otázkou Microsoft Q&](/answers/topics/azure-data-factory.html)