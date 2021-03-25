---
title: Používání účtů úložiště spravovaných zákazníkem v Log Analytics ve službě Azure Monitor
description: Použití vlastního účtu úložiště pro Log Analytics scénáře
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: a6d4c5811c08aa8c4de2eeea5f5f53967c3006b2
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025352"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Používání účtů úložiště spravovaných zákazníkem v Log Analytics ve službě Azure Monitor

Log Analytics spoléhá na Azure Storage v různých scénářích. Toto použití se obvykle spravuje automaticky. Některé případy ale vyžadují, abyste poskytovali a spravovali vlastní účet úložiště, označovaný také jako účet úložiště spravovaný zákazníkem. Tento dokument popisuje použití úložiště spravovaného zákazníkem pro protokoly WAD/LAD, privátní odkaz a šifrování CMK (Customer-Managed Key). 

> [!NOTE]
> Doporučujeme, abyste nedostali závislost na obsahu Log Analytics nahrávají do úložiště spravovaného zákazníkem, protože se může změnit formátování a obsah.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Ingestování protokolů rozšíření Azure Diagnostics (WAD/LAD)
Agenti rozšíření Azure Diagnostics (označované také jako WAD a LAD agenti pro systémy Windows a Linux) shromažďují různé protokoly operačního systému a ukládají je do účtu úložiště spravovaného zákazníky. Pak můžete tyto protokoly ingestovat do Log Analytics, abyste je zkontrolovali a analyzovali.
### <a name="how-to-collect-azure-diagnostics-extension-logs-from-your-storage-account"></a>Jak shromažďovat protokoly rozšíření Azure Diagnostics z účtu úložiště
Připojte účet úložiště k pracovnímu prostoru Log Analytics jako zdroj dat úložiště pomocí [Azure Portal](../agents/diagnostics-extension-logs.md#collect-logs-from-azure-storage) nebo zavoláním [rozhraní API služby Storage Insights](/rest/api/loganalytics/storage%20insights/createorupdate).

Podporované datové typy:
* Syslog
* Události systému Windows
* Service Fabric
* Události ETW
* Protokoly IIS

## <a name="using-private-links"></a>Používání privátních odkazů
Účty úložiště spravované zákazníkem se používají k ingestování vlastních protokolů nebo protokolů IIS, když se k připojení Azure Monitorch prostředků používají privátní odkazy. Proces ingestování těchto datových typů nejprve nahraje protokoly do zprostředkujícího Azure Storage účtu a pak je ingestuje do pracovního prostoru. 

### <a name="using-a-customer-managed-storage-account-over-a-private-link"></a>Použití účtu úložiště spravovaného zákazníkem přes soukromý odkaz
#### <a name="workspace-requirements"></a>Požadavky na pracovní prostor
Pokud se připojujete k Azure Monitor přes privátní odkaz, agenti Log Analytics můžou posílat protokoly jenom k pracovním prostorům dostupným prostřednictvím privátního odkazu. Tento požadavek znamená, že byste měli:
* Konfigurace objektu Azure Monitor oboru privátního propojení (AMPLS)
* Připojení k pracovním prostorům
* Připojte AMPLS k síti přes privátní odkaz. 

Další informace o postupu konfigurace AMPLS najdete v tématu [použití privátního odkazu Azure k bezpečnému připojení sítí k Azure monitor](./private-link-security.md). 

#### <a name="storage-account-requirements"></a>Požadavky na účet úložiště
Aby se účet úložiště mohl úspěšně připojit k privátnímu propojení, musí:
* Být umístěn ve vaší virtuální síti nebo v partnerské síti a připojen k virtuální síti prostřednictvím privátního propojení.
* Být umístěn ve stejné oblasti jako pracovní prostor, ke kterému je propojen.
* Povolí Azure Monitor přístup k účtu úložiště. Pokud se rozhodnete, že chcete přístup k účtu úložiště vybrat jenom sítě, měli byste vybrat výjimku: "" udělit přístup k tomuto účtu úložiště důvěryhodným službám Microsoftu ".
![Obrázek důvěryhodné služby MS účtu úložiště](./media/private-storage/storage-trust.png)
* Pokud váš pracovní prostor zpracovává provoz z jiných sítí, měli byste nakonfigurovat účet úložiště tak, aby povoloval příchozí provoz pocházející z příslušných sítí nebo Internetu.
* Koordinovat verzi TLS mezi agenty a účtem úložiště – doporučujeme odesílat data Log Analytics pomocí TLS 1,2 nebo vyšší. Projděte si [pokyny pro konkrétní platformu](./data-security.md#sending-data-securely-using-tls-12)a v případě potřeby [Nakonfigurujte agenty na používání protokolu TLS 1,2](../agents/agent-windows.md#configure-agent-to-use-tls-12). Pokud z nějakého důvodu není možné, nakonfigurujte účet úložiště tak, aby přijímal protokol TLS 1,0.

### <a name="using-a-customer-managed-storage-account-for-cmk-data-encryption"></a>Použití účtu úložiště spravovaného zákazníkem pro šifrování dat CMK
Azure Storage šifruje všechna neaktivní neaktivní data v účtu úložiště. Ve výchozím nastavení používá k šifrování dat klíče spravované Microsoftem (MMK). Azure Storage ale taky umožňuje použít k šifrování dat úložiště CMK z trezoru klíčů Azure. Můžete buď importovat vlastní klíče do Azure Key Vault, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů.
#### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>Scénáře CMK, které vyžadují účet úložiště spravovaný zákazníkem
* Šifrování dotazů s výstrahami protokolů pomocí CMK
* Šifrování uložených dotazů pomocí CMK

#### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>Jak použít CMK na účty úložiště spravované zákazníkem
##### <a name="storage-account-requirements"></a>Požadavky na účet úložiště
Účet úložiště a trezor klíčů musí být ve stejné oblasti, ale můžou být v různých předplatných. Další informace o Azure Storage šifrování a správě klíčů najdete v části [Azure Storage šifrování pro neaktivní data](../../storage/common/storage-service-encryption.md).

##### <a name="apply-cmk-to-your-storage-accounts"></a>Použití CMK pro vaše účty úložiště
Pokud chcete Azure Storage účet nakonfigurovat tak, aby používal CMK s Azure Key Vault, použijte [Azure Portal](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json), [PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)nebo rozhraní příkazového [řádku](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json). 

## <a name="link-storage-accounts-to-your-log-analytics-workspace"></a>Připojení účtů úložiště k pracovnímu prostoru Log Analytics
### <a name="using-the-azure-portal"></a>Použití webu Azure Portal
V nabídce Azure Portal otevřete nabídku pracovní prostor a vyberte *propojené účty úložiště*. Otevře se okno s propojenými účty úložiště pomocí výše zmíněných případů použití (příjem prostřednictvím privátního propojení, použití CMK u uložených dotazů nebo výstrah).
![Obrázek okna propojené účty úložiště ](./media/private-storage/all-linked-storage-accounts.png) : Když vyberete položku v tabulce, otevře se podrobnosti o svém účtu úložiště, kde můžete nastavit nebo aktualizovat propojený účet úložiště pro tento typ. 
![Propojte obrázek v okně účtu úložiště ](./media/private-storage/link-a-storage-account-blade.png) . Pokud chcete, můžete použít stejný účet pro různé případy použití.

### <a name="using-the-azure-cli-or-rest-api"></a>Použití rozhraní příkazového řádku Azure nebo REST API
Účet úložiště můžete propojit taky s vaším pracovním prostorem prostřednictvím rozhraní příkazového [řádku Azure CLI](/cli/azure/monitor/log-analytics/workspace/linked-storage) nebo [REST API](/rest/api/loganalytics/linkedstorageaccounts).

Platné hodnoty DataSourceType jsou:
* CustomLogs – použití účtu úložiště pro vlastní protokoly a ingestování protokolů IIS
* Dotaz – použití účtu úložiště k uložení uložených dotazů (vyžaduje se pro šifrování CMK)
* Výstrahy – použití účtu úložiště k ukládání výstrah založených na protokolu (vyžaduje se pro šifrování CMK)


## <a name="managing-linked-storage-accounts"></a>Správa propojených účtů úložiště

### <a name="create-or-modify-a-link"></a>Vytvoří nebo upraví odkaz.
Když propojíte účet úložiště s pracovním prostorem, Log Analytics ho začít používat místo účtu úložiště, který vlastní služba. Můžeš 
* Registrace více účtů úložiště pro rozložení zatížení protokolů mezi nimi
* Opětovné použití stejného účtu úložiště pro několik pracovních prostorů

### <a name="unlink-a-storage-account"></a>Zrušení propojení účtu úložiště
Pokud chcete ukončit používání účtu úložiště, odpojte úložiště od pracovního prostoru. Odpojení všech účtů úložiště z pracovního prostoru znamená, Log Analytics se pokusí spoléhat na účty úložiště spravované službou. Pokud má vaše síť omezený přístup k Internetu, nemusí být tato úložiště k dispozici a žádný scénář, který závisí na úložišti, selže.

### <a name="replace-a-storage-account"></a>Výměna účtu úložiště
Chcete-li nahradit účet úložiště, který se používá pro přijímání,
1.  **Vytvoří odkaz na nový účet úložiště.** Agenti protokolování budou mít aktualizovanou konfiguraci a začnou odesílat data do nového úložiště také. Proces může trvat několik minut.
2.  **Pak zrušte propojení starého účtu úložiště, aby agenti přestali zapisovat na odebraný účet.** Proces příjmu uchovává data z tohoto účtu, dokud není vše ingestované. Účet úložiště neodstraňujte, dokud neuvidíte, že se všechny protokoly ingestují.

### <a name="maintaining-storage-accounts"></a>Údržba účtů úložiště
#### <a name="manage-log-retention"></a>Správa uchovávání protokolů
Při používání vlastního účtu úložiště je uchování až po vás. Log Analytics neodstraní protokoly uložené v privátním úložišti. Místo toho byste měli nastavit zásadu pro zpracování zatížení podle vašich požadavků.

#### <a name="consider-load"></a>Zvažte načtení
Účty úložiště mohou zpracovávat určité zatížení požadavků na čtení a zápis před tím, než začnou žádosti o omezení (Další informace najdete v tématu [škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](../../storage/common/scalability-targets-standard-account.md)). Omezení ovlivňuje dobu potřebnou k přijímání protokolů. Pokud je váš účet úložiště přetížený, zaregistrujte si další účet úložiště, abyste mezi nimi mohli zatížení rozložit. Pokud chcete monitorovat kapacitu a výkon svého účtu úložiště, Projděte si jeho [přehledy na Azure Portal]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview).

### <a name="related-charges"></a>Související poplatky
Účty úložiště se účtují podle objemu uložených dat, typu úložiště a typu redundance. Podrobnosti najdete v tématu [ceny objektů blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs) a [ceny Table Storage](https://azure.microsoft.com/pricing/details/storage/tables).


## <a name="next-steps"></a>Další kroky

- Přečtěte si [, jak pomocí privátního propojení Azure bezpečně propojit sítě a Azure monitor](private-link-security.md)
- Další informace o [Azure monitor klíčů spravovaných zákazníkem](../logs/customer-managed-keys.md)