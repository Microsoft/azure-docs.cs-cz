---
title: Používání účtů úložiště spravovaných zákazníkem v Azure Monitor Log Analytics
description: Použití vlastního účtu úložiště pro Log Analytics scénáře
ms.subservice: logs
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: a487e6989792c63aaf5baf9ddb3875df549561a4
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143987"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Používání účtů úložiště spravovaných zákazníkem v Azure Monitor Log Analytics

Log Analytics spoléhá na Azure Storage v nejrůznějších scénářích. Toto použití se obvykle spravuje automaticky. Některé případy ale vyžadují, abyste poskytovali a spravovali vlastní účet úložiště, označovaný také jako účet úložiště spravovaný zákazníkem. Tento dokument popisuje použití úložiště spravovaného zákazníkem pro ingestování protokolů WAD/LAD, scénářů specifických pro privátní propojení a šifrování CMK. 

> [!NOTE]
> Doporučujeme, abyste nedostali závislost na obsahu Log Analytics nahrávají do úložiště spravovaného zákazníkem, protože se může změnit formátování a obsah.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Ingestování protokolů rozšíření Azure Diagnostics (WAD/LAD)
Agenti rozšíření Azure Diagnostics (označované také jako WAD a LAD agenti pro systémy Windows a Linux) shromažďují různé protokoly operačního systému a ukládají je do účtu úložiště spravovaného zákazníky. Pak můžete tyto protokoly ingestovat do Log Analytics, abyste je zkontrolovali a analyzovali.
Jak shromažďovat protokoly rozšíření Azure Diagnostics z účtu úložiště připojte účet úložiště k vašemu pracovnímu prostoru Log Analytics jako zdroj dat úložiště pomocí [Azure Portal](./diagnostics-extension-logs.md#collect-logs-from-azure-storage) nebo zavoláním [rozhraní API služby Storage Insights](/rest/api/loganalytics/connectedsources/storage%20insights/createorupdate).

Podporované datové typy:
* Syslog
* Události systému Windows
* Service Fabric
* Události ETW
* Protokoly IIS

## <a name="using-private-links"></a>Používání privátních odkazů
V některých případech použití se vyžadují účty úložiště spravované zákazníkem, když se k připojení Azure Monitorch prostředků používají privátní odkazy. Jedním z takových případů je přijímání vlastních protokolů nebo protokolů IIS. Tyto datové typy se nejdřív nahrají jako objekty blob pro zprostředkující Azure Storage účet a pak se ingestují do pracovního prostoru. Podobně některá Azure Monitor řešení můžou používat účty úložiště k ukládání velkých souborů, jako jsou například soubory výpisu v programu Watson, které používá řešení Azure Security Center. 

##### <a name="private-link-scenarios-that-require-a-customer-managed-storage"></a>Scénáře privátních odkazů, které vyžadují úložiště spravované zákazníkem
* Přijímání vlastních protokolů a protokolů IIS
* Povolení řešení ASC pro shromažďování souborů výpisu z programu Watson

### <a name="how-to-use-a-customer-managed-storage-account-over-a-private-link"></a>Použití účtu úložiště spravovaného zákazníkem přes privátní propojení
##### <a name="workspace-requirements"></a>Požadavky na pracovní prostor
Pokud se připojujete k Azure Monitor přes privátní odkaz, agenti Log Analytics můžou odesílat protokoly do pracovních prostorů propojených s vaší sítí přes privátní odkaz. Toto pravidlo vyžaduje, abyste správně nakonfigurovali Azure Monitor objekt oboru privátního propojení (AMPLS), připojili ho k vašim pracovním prostorům a pak připojili AMPLS k síti přes privátní odkaz. Další informace o postupu konfigurace AMPLS najdete v tématu [použití privátního odkazu Azure k bezpečnému připojení sítí k Azure monitor](./private-link-security.md). 
##### <a name="storage-account-requirements"></a>Požadavky na účet úložiště
Aby se účet úložiště mohl úspěšně připojit k privátnímu propojení, musí:
* Musí se nacházet ve vaší virtuální síti nebo v partnerské síti a připojená k virtuální síti prostřednictvím privátního propojení. To umožňuje agentům ve vaší virtuální síti odesílat protokoly do účtu úložiště.
* Být umístěn ve stejné oblasti jako pracovní prostor, ke kterému je propojen.
* Povolí Azure Monitor přístup k účtu úložiště. Pokud se rozhodnete, že chcete pro přístup k účtu úložiště vybrat jenom sítě, měli byste taky tuto výjimku zakázat: "Povolte přístup k tomuto účtu úložiště důvěryhodným službám Microsoftu". To umožňuje Log Analytics číst protokoly ingestované do tohoto účtu úložiště.
* Pokud váš pracovní prostor zpracovává provoz z jiných sítí, měli byste nakonfigurovat účet úložiště tak, aby povoloval příchozí provoz pocházející z příslušných sítí nebo Internetu.

##### <a name="link-your-storage-account-to-a-log-analytics-workspace"></a>Propojení účtu úložiště s Log Analytics pracovním prostorem
Svůj účet úložiště můžete propojit s pracovním prostorem prostřednictvím rozhraní příkazového [řádku Azure CLI](/cli/azure/monitor/log-analytics/workspace/linked-storage) nebo [REST API](/rest/api/loganalytics/linkedstorageaccounts). Platné hodnoty DataSourceType:
* CustomLogs – pro použití úložiště pro vlastní protokoly a protokoly IIS během přijímání.
* AzureWatson – použijte úložiště pro soubory výpisu paměti programu Watson odeslané řešením ASC (Azure Security Center). Další informace o správě uchovávání, nahrazení propojeného účtu úložiště a sledování aktivity účtu úložiště najdete v tématu [Správa propojených účtů úložiště](#managing-linked-storage-accounts). 

## <a name="encrypting-data-with-cmk"></a>Šifrování dat pomocí CMK
Azure Storage šifruje všechna neaktivní neaktivní data v účtu úložiště. Ve výchozím nastavení šifruje data pomocí klíčů spravovaných Microsoftem (MMK). Azure Storage ale místo toho umožní šifrovat data úložiště pomocí klíče spravovaného zákazníkem (CMK) z trezoru klíčů Azure. Můžete buď importovat vlastní klíče do Azure Key Vault, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů.
##### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>Scénáře CMK, které vyžadují účet úložiště spravovaný zákazníkem
* Šifrování dotazů s výstrahami protokolů pomocí CMK
* Šifrování uložených dotazů pomocí CMK

### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>Jak použít CMK na účty úložiště spravované zákazníkem
##### <a name="storage-account-requirements"></a>Požadavky na účet úložiště
Účet úložiště a trezor klíčů musí být ve stejné oblasti, ale můžou být v různých předplatných. Další informace o Azure Storage šifrování a správě klíčů najdete v části [Azure Storage šifrování pro neaktivní data](../../storage/common/storage-service-encryption.md).

##### <a name="apply-cmk-to-your-storage-accounts"></a>Použití CMK pro vaše účty úložiště
Pokud chcete nakonfigurovat Azure Storage účet pro použití klíčů spravovaných zákazníkem s Azure Key Vault, použijte [Azure Portal](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json), [PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json) nebo rozhraní příkazového [řádku](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json). 

## <a name="managing-linked-storage-accounts"></a>Správa propojených účtů úložiště

Pokud chcete připojit nebo zrušit propojení účtů úložiště s vaším pracovním prostorem, použijte rozhraní příkazového [řádku Azure](/cli/azure/monitor/log-analytics/workspace/linked-storage) nebo [REST API](/rest/api/loganalytics/linkedstorageaccounts).

##### <a name="create-or-modify-a-link"></a>Vytvoří nebo upraví odkaz.
Když propojíte účet úložiště s pracovním prostorem, Log Analytics ho začít používat místo účtu úložiště, který vlastní služba. Můžeš 
* Registrace více účtů úložiště pro rozložení zatížení protokolů mezi nimi
* Opětovné použití stejného účtu úložiště pro několik pracovních prostorů

##### <a name="unlink-a-storage-account"></a>Zrušení propojení účtu úložiště
Pokud chcete ukončit používání účtu úložiště, odpojte úložiště od pracovního prostoru. Odpojení všech účtů úložiště z pracovního prostoru znamená, Log Analytics se pokusí spoléhat na účty úložiště spravované službou. Pokud má vaše síť omezený přístup k Internetu, nemusí být tato úložiště k dispozici a žádný scénář, který závisí na úložišti, selže.

##### <a name="replace-a-storage-account"></a>Výměna účtu úložiště
Chcete-li nahradit účet úložiště, který se používá pro přijímání,
1.  **Vytvoří odkaz na nový účet úložiště.** Agenti protokolování budou mít aktualizovanou konfiguraci a začnou odesílat data do nového úložiště také. Proces může trvat několik minut.
2.  **Pak zrušte propojení starého účtu úložiště, aby agenti přestali zapisovat na odebraný účet.** Proces příjmu uchovává data z tohoto účtu, dokud není vše ingestované. Účet úložiště neodstraňujte, dokud neuvidíte, že se všechny protokoly ingestují.

### <a name="maintaining-storage-accounts"></a>Údržba účtů úložiště
##### <a name="manage-log-retention"></a>Správa uchovávání protokolů
Při používání vlastního účtu úložiště je uchování až po vás. Jinými slovy Log Analytics neodstraní protokoly uložené v privátním úložišti. Místo toho byste měli nastavit zásadu pro zpracování zatížení podle vašich požadavků.

##### <a name="consider-load"></a>Zvažte načtení
Účty úložiště mohou zpracovávat určité zatížení požadavků na čtení a zápis před tím, než začnou žádosti o omezení (viz téma [škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](../../storage/common/scalability-targets-standard-account.md) , kde najdete další podrobnosti). Omezení ovlivňuje dobu potřebnou k přijímání protokolů. Pokud je váš účet úložiště přetížený, zaregistrujte si další účet úložiště, abyste mezi nimi mohli zatížení rozložit. Pokud chcete monitorovat kapacitu a výkon svého účtu úložiště, Projděte si jeho [přehledy na Azure Portal]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview).

### <a name="related-charges"></a>Související poplatky
Účty úložiště se účtují podle objemu uložených dat, typu úložiště a typu redundance. Podrobnosti najdete v tématu [ceny objektů blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs) a [ceny Table Storage](https://azure.microsoft.com/pricing/details/storage/tables).


## <a name="next-steps"></a>Další kroky

- Přečtěte si [, jak pomocí privátního propojení Azure bezpečně propojit sítě a Azure monitor](private-link-security.md)
- Další informace o [Azure monitor klíčů spravovaných zákazníkem](customer-managed-keys.md)