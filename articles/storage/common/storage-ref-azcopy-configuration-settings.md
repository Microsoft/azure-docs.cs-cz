---
title: Nastavení konfigurace AzCopy v10 za účelem (Azure Storage) | Microsoft Docs
description: Tento článek popisuje referenční informace o nastaveních konfigurace AzCopy v10 za účelem.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4fcbcf145dc417d2a7f78913e70429c3929cd902
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509052"
---
# <a name="azcopy-v10-configuration-settings-azure-storage"></a>Nastavení konfigurace AzCopy v10 za účelem (Azure Storage)

AzCopy je nástroj příkazového řádku, pomocí kterého můžete kopírovat objekty blob nebo soubory do nebo z účtu úložiště. Tento článek obsahuje seznam proměnných prostředí, které můžete použít ke konfiguraci AzCopy v10 za účelem.

> [!NOTE]
> Pokud hledáte obsah, který vám pomůžete začít s AzCopy, přečtěte si téma Začínáme [s AzCopy](storage-use-azcopy-v10.md).

## <a name="azcopy-v10-environment-variables"></a>Proměnné prostředí AzCopy v10 za účelem

Následující tabulka popisuje jednotlivé proměnné prostředí a poskytuje odkazy na obsah, který vám může pomáhat s použitím proměnné.

| Proměnná prostředí | Popis |
|--|--|
| AWS_ACCESS_KEY_ID | Amazon Web Services přístupový klíč. Poskytuje klíč pro autorizaci pomocí Amazon Web Services. [Kopírování dat z Amazon S3 do Azure Storage pomocí AzCopy](storage-use-azcopy-s3.md) |
| AWS_SECRET_ACCESS_KEY | Amazon Web Services tajný klíč přístupu poskytuje tajný klíč k autorizaci pomocí Amazon Web Services. [Kopírování dat z Amazon S3 do Azure Storage pomocí AzCopy](storage-use-azcopy-s3.md) |
| AZCOPY_ACTIVE_DIRECTORY_ENDPOINT | Koncový bod Azure Active Directory, který se má použít. Tato proměnná se používá jenom pro automatické přihlašování. při vyvolání příkazu pro přihlášení použijte prosím příznak příkazového řádku. |
| AZCOPY_AUTO_LOGIN_TYPE | Nastavte tuto proměnnou na `DEVICE` , `MSI` nebo `SPN` . Tato proměnná poskytuje možnost autorizace bez použití `azcopy login` příkazu. Tento mechanismus je užitečný v případech, kdy váš operační systém nemá tajné úložiště, jako je například Linux *klíčů*. Viz [autorizace bez tajného úložiště](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_BUFFER_GB | Zadejte maximální velikost systémové paměti, kterou má AzCopy použít při stahování a nahrávání souborů. Vyjádřete tuto hodnotu v gigabajtech (GB). Viz [optimalizace využití paměti](storage-use-azcopy-optimize.md#optimize-memory-use) |
| AZCOPY_CACHE_PROXY_LOOKUP | Ve výchozím nastavení se AzCopy ve Windows do mezipaměti zařadí proxy server vyhledávání na úrovni hostitele (nepřijímá se cesta URL k účtu). Pokud chcete zakázat mezipaměť, nastavte na jinou hodnotu než true. |
| AZCOPY_CONCURRENCY_VALUE | Určuje počet souběžných požadavků, které mohou nastat. Tuto proměnnou můžete použít k zvýšení propustnosti. Pokud má počítač méně než 5 procesorů, pak je hodnota této proměnné nastavena na `32` . Jinak se výchozí hodnota rovná 16násobku počtu CPU. Maximální výchozí hodnota této proměnné je `3000` , ale tuto hodnotu můžete nastavit ručně nebo dolů. Viz [zvýšení souběžnosti](storage-use-azcopy-optimize.md#increase-concurrency) |
| AZCOPY_CONCURRENT_FILES | Potlačí (přibližný) počet souborů, které jsou právě zpracovávány, tím, že řídí, kolik souborů souběžně zahajuje přenosy. |
| AZCOPY_CONCURRENT_SCAN | Určuje stupeň (max) paralelismu používaných při skenování. Ovlivňuje jenom paralelně výčty, mezi které patří soubory Azure a objekty BLOB a místní souborové systémy. |
| AZCOPY_DEFAULT_SERVICE_API_VERSION | Přepíše verzi rozhraní API služby tak, aby AzCopy mohla vyhovovat vlastním prostředím, jako je Azure Stack. |
| AZCOPY_DISABLE_HIERARCHICAL_SCAN | Platí jenom v případě, že je zdrojem objektů blob Azure. Souběžná kontrola je rychlejší, ale využívá rozhraní API hierarchického výpisu, což může vést k většímu počtu IOs a nákladů. Zadejte hodnotu true, pokud chcete snížit výkon, ale Ušetřete náklady. |
| AZCOPY_JOB_PLAN_LOCATION | Přepíše, kde jsou uloženy soubory plánu úlohy (používané pro sledování průběhu a obnovení), aby nedošlo k zaplnění disku. |
| AZCOPY_LOG_LOCATION | Přepíše místo, kde jsou uloženy soubory protokolu, aby nedošlo k zaplnění disku. |
| AZCOPY_MSI_CLIENT_ID | ID klienta spravované identity přiřazené uživatelem. Použijte, pokud `AZCOPY_AUTO_LOGIN_TYPE` je nastavené na `MSI` . Viz [autorizace bez tajného úložiště](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_MSI_OBJECT_ID | ID objektu spravované identity přiřazené uživatelem. Použijte, pokud `AZCOPY_AUTO_LOGIN_TYPE` je nastavené na `MSI` . Viz [autorizace bez tajného úložiště](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_MSI_RESOURCE_STRING | ID prostředku spravované identity přiřazené uživatelem. Viz [autorizace bez tajného úložiště](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_PACE_PAGE_BLOBS | Měla by být propustnost pro objekty blob stránky automaticky upravena tak, aby odpovídala limitům služby? Platí výchozí hodnota. Pokud chcete zakázat, nastavte na hodnotu false. |
| AZCOPY_PARALLEL_STAT_FILES | Způsobí, že nástroj AzCopy při kontrole místního systému souborů vyhledá vlastnosti souboru na paralelních vláknech.  Vlákna jsou vykreslena z fondu definovaném AZCOPY_CONCURRENT_SCAN.  Nastavení na hodnotu true může zlepšit výkon kontroly na platformě Linux.  Ve Windows není potřeba nebo se nedoporučuje. |
| AZCOPY_SHOW_PERF_STATES | Pokud je nastaveno na cokoli, bude výstup na obrazovce zahrnovat počty bloků podle stavu. |
| AZCOPY_SPA_APPLICATION_ID | ID aplikace pro registraci aplikace objektu služby Použijte, pokud `AZCOPY_AUTO_LOGIN_TYPE` je nastavené na `SPN` . Viz [autorizace bez tajného úložiště](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CERT_PASSWORD | Heslo certifikátu. Použijte, pokud `AZCOPY_AUTO_LOGIN_TYPE` je nastavené na `SPN` . Viz [autorizace bez tajného úložiště](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CERT_PATH | Relativní nebo plně kvalifikovaná cesta k souboru certifikátu. Použijte, pokud `AZCOPY_AUTO_LOGIN_TYPE` je nastavené na `SPN` . Viz [autorizace bez tajného úložiště](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CLIENT_SECRET | Tajný kód klienta. Použijte, pokud `AZCOPY_AUTO_LOGIN_TYPE` je nastavené na `SPN` . Viz [autorizace bez tajného úložiště](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_TENANT_ID | ID tenanta Azure Active Directory, které se má použít pro interaktivní přihlášení zařízení OAuth. Tato proměnná se používá jenom pro automatické přihlašování. při vyvolání příkazu pro přihlášení použijte prosím příznak příkazového řádku. |
| AZCOPY_TUNE_TO_CPU | Nastavte na hodnotu false, pokud chcete, aby AzCopy při automatického ladění své úrovně souběžnosti (například v příkazu srovnávacího testu) nemohlo brát v úvahu využití CPU. |
| AZCOPY_USER_AGENT_PREFIX | Přidejte předponu k výchozímu uživatelskému agentu AzCopy, který se používá pro účely telemetrie. Automaticky se vloží mezera. |
| GOOGLE_APPLICATION_CREDENTIALS | Absolutní cesta k souboru klíče účtu služby poskytuje klíč pro autorizaci s úložištěm Google Cloud. [Kopírování dat ze služby Google Cloud Storage do Azure Storage pomocí AzCopy (Preview)](storage-ref-azcopy-configuration-settings.md) |
| HTTPS_PROXY | Konfiguruje nastavení proxy serveru pro AzCopy. Nastavte tuto proměnnou na IP adresu proxy serveru a číslo portu proxy serveru. Například, `xx.xxx.xx.xxx:xx`. Pokud používáte AzCopy ve Windows, AzCopy automaticky detekuje nastavení proxy serveru, takže ve Windows toto nastavení používat nemusíte. Pokud se rozhodnete toto nastavení použít ve Windows, přepíše se tím automatická detekce. Viz [Konfigurace nastavení proxy serveru](#configure-proxy-settings) . |


## <a name="configure-proxy-settings"></a>Konfigurace nastavení proxy serveru

Chcete-li nakonfigurovat nastavení proxy serveru pro AzCopy, nastavte `HTTPS_PROXY` proměnnou prostředí. Pokud používáte AzCopy ve Windows, AzCopy automaticky detekuje nastavení proxy serveru, takže ve Windows toto nastavení používat nemusíte. Pokud se rozhodnete toto nastavení použít ve Windows, přepíše se tím automatická detekce.

| Operační systém | Příkaz  |
|--------|-----------|
| **Windows** | V příkazovém řádku použijte: `set HTTPS_PROXY=<proxy IP>:<proxy port>`<br> V prostředí PowerShell použijte: `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

AzCopy v současné době nepodporuje proxy servery, které vyžadují ověřování pomocí protokolu NTLM nebo Kerberos.

### <a name="bypassing-a-proxy"></a>Obejití proxy serveru

Pokud používáte AzCopy ve Windows a chcete mu sdělit, aby nepoužívala _žádný_ proxy server (místo automatického zjišťování nastavení), použijte tyto příkazy. S těmito nastaveními nebude AzCopy vyhledávat ani se pokoušet použít žádný proxy server.

| Operační systém | Prostředí | Příkazy  |
|--------|-----------|----------|
| **Windows** | Příkazový řádek (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

V jiných operačních systémech stačí ponechat proměnnou HTTPS_PROXY zrušit, pokud chcete použít žádný proxy server.

## <a name="see-also"></a>Viz také

- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)
- [Optimalizujte výkon AzCopy v10 za účelem pomocí Azure Storage](storage-use-azcopy-optimize.md)
- [Řešení potíží s AzCopy v10 za účelem v Azure Storage pomocí souborů protokolu](storage-use-azcopy-configure.md)
