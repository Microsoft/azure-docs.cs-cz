---
title: Pokyny pro zotavení po havárii pro avere vFXT pro Azure
description: Jak chránit data v avere vFXT pro Azure před náhodným odstraněním nebo výpadky
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 5cc4678b082aa5a4a3f90518ff8fac448f414f1a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92342242"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Pokyny pro zotavení po havárii pro avere vFXT pro Azure

Tento článek popisuje strategie ochrany avere vFXT pro pracovní postup Azure a poskytuje pokyny k zálohování dat, abyste se mohli zotavit z havárií nebo výpadků.

Avere vFXT pro Azure dočasně ukládá data do mezipaměti. Data se ukládají dlouhodobě v systémech back-endu – v místních hardwarových systémech, kontejnerech úložiště objektů BLOB v Azure nebo v obou.

Chcete-li chránit před výpadky a možnou ztrátou dat, vezměte v úvahu tyto čtyři oblasti:

* Ochrana proti výpadkům, pokud avere vFXT pro systém Azure nebude k dispozici
* Ochrana dat v mezipaměti clusteru
* Ochrana dat v cloudovém úložišti back-endu NAS
* Ochrana dat v cloudovém úložišti Azure BLOB v back-endu

Každý avere vFXT pro zákazníky Azure musí vytvořit vlastní komplexní plán zotavení po havárii, který obsahuje plány pro tyto položky. Odolnost můžete také vytvářet v aplikacích, které používáte s clusterem vFXT. Pro nápovědu si přečtěte odkazy v části [Další kroky](#next-steps) .

## <a name="protect-against-downtime"></a>Ochrana před výpadky

Redundance je integrovaná do avere vFXT pro produkt Azure:

* Cluster je vysoce dostupný a jednotlivé uzly clusteru můžou převzít služby při selhání s minimálním přerušením.
* Data změněná v mezipaměti jsou pravidelně zapisována do back-endu Core filers (hardware NAS nebo Azure BLOB) pro dlouhodobé uložení.

Každý avere vFXT pro cluster Azure se musí nacházet v jedné zóně dostupnosti, ale můžete použít redundantní clustery nacházející se v různých zónách nebo v různých oblastech k rychlému poskytnutí přístupu v případě regionálního výpadku.

Pokud vás zajímá o ztrátě přístupu k datům, můžete kontejnery úložiště umístit i do několika oblastí. Mějte ale na paměti, že transakce mezi oblastmi mají vyšší latenci a vyšší náklady než transakce, které jsou v určité oblasti.

## <a name="protect-data-in-the-cluster-cache"></a>Ochrana dat v mezipaměti clusteru

Data uložená v mezipaměti jsou vždy před pravidelným vypnutím zapsána do základního filers, ale v nekontrolovaném vypnutí může dojít ke ztrátě změněných dat v mezipaměti.

Pokud cluster použijete k optimalizaci jenom pro čtení souborů, neztratí se žádné změny. Pokud použijete cluster k ukládání změn souborů (zápisy) do mezipaměti, zvažte, jestli se mají upravit základní [zásady mezipaměti](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html) filers.<!-- link to legacy doc --> k přizpůsobení četnosti zápisu dat do dlouhodobého úložiště.

Obecně platí, že by se měl plán obnovení zaměřit na zálohování systémů úložiště back-end, které obsahují více dat a jsou obvykle důležitější pro opětovné vytvoření pracovního postupu po selhání.

## <a name="protect-data-in-nas-core-filers"></a>Ochrana dat na serveru NAS Core filers

Pomocí akceptovaných metod můžete chránit data uložená na místním serveru NAS souborového hardware Core, včetně snímků a úplných záloh, jak doporučil poskytovatel serveru NAS. Zotavení po havárii pro tyto základní filers je nad rámec tohoto článku.

## <a name="protect-data-in-azure-blob-storage"></a>Ochrana dat v úložišti objektů BLOB v Azure

Avere vFXT pro Azure používá místně redundantní úložiště (LRS) pro Azure Blob Core filers. To znamená, že data v kontejnerech objektů BLOB se automaticky zkopírují do ochrany proti přechodným chybám hardwaru v rámci datového centra.

V této části najdete tipy, jak dále chránit data v úložišti objektů BLOB z vzácných výpadků nebo náhodných odstranění v rámci oblastí.

Mezi osvědčené postupy při ochraně dat v úložišti objektů BLOB v Azure patří:

* Zkopírujte důležitá data do jiného účtu úložiště v jiné oblasti často (jak je to určeno podle plánu zotavení po havárii).
* Řízení přístupu k datům ze všech cílových systémů, aby nedocházelo k náhodnému odstranění nebo poškození. Zvažte použití [zámků prostředků](../azure-resource-manager/management/lock-resources.md) v úložišti dat.
* Povolte funkci avere vFXT for Azure [Cloud Snapshot](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) pro váš objekt BLOB Core filers.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Kopírování avere dat vFXT Core souborového do účtu zálohy

Pomocí těchto kroků můžete vytvořit zálohu dat v jiném účtu.

1. V případě potřeby Vygenerujte nový šifrovací klíč a bezpečně ho uložte mimo příslušné systémy.

   Pokud vaše data šifrují cluster avere vFXT pro Azure, měli byste před kopírováním dat do jiného účtu úložiště vygenerovat nový šifrovací klíč. Bezpečně uložte tento klíč a heslo do zařízení, které je bezpečné a které nebude mít vliv na regionální selhání.

   Tento klíč je potřeba zadat při přidávání kontejneru do clusteru – i když ho znovu přidáte do původního clusteru.

   Číst [Nastavení cloudového šifrování](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> pro podrobné informace.

   Pokud Váš kontejner používá jenom integrované šifrování Azure, můžete tento krok přeskočit.

1. Odeberte základní souborového ze systému. To vynutí, aby cluster zapisoval všechna změněná data do back-endu úložiště.

   I když budete muset po zálohování znovu přidat základní souborového, je odebrání tohoto nejlepšího způsobu, jak zaručit, že všechna data jsou zcela zapsána do back-endu. (Možnost pozastavit může někdy ponechat změněná data v mezipaměti.) <!-- xxx true? or just metadata? -->

   Poznamenejte si název a informace o spojení základní souborového (uvedené na stránce **oboru názvů** v Ovládacích panelech), abyste je mohli replikovat po opětovném přidání kontejneru po zálohování.

   Základní souborového můžete odebrat pomocí ovládacího panelu clusteru. [Otevřete ovládací panel cluster](avere-vfxt-cluster-gui.md) a vyberte **Core souborového**  >  **Spravovat Core filers**. Najděte systém úložiště, který chcete zálohovat, a pomocí tlačítka **Odebrat** ho z clusteru odstraňte.

1. Vytvořte nový prázdný kontejner úložiště objektů BLOB v jiném účtu úložiště v jiné oblasti.

1. K zkopírování dat do základního souborového do nového kontejneru použijte libovolný pohodlný nástroj pro kopírování. Kopie musí replikovat data beze změn a bez přerušení vlastního formátu cloudového systému souborů, který používá avere vFXT pro Azure. Nástroje založené na Azure zahrnují [AzCopy](../storage/common/storage-use-azcopy-v10.md), [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)a [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

1. Po zkopírování dat do kontejneru zálohování přidejte původní kontejner zpátky do clusteru, jak je popsáno v tématu [Konfigurace úložiště](avere-vfxt-add-storage.md).

   * Použijte stejný základní název souborového a informace o spojení, aby se pracovní postupy klienta nemusely měnit.
   * Nastavte hodnotu **obsah kontejneru** na možnost stávající data.
   * Pokud byl kontejner zašifrovaný clusterem, je nutné zadat aktuální šifrovací klíč pro jeho obsah. (Jedná se o klíč, který jste aktualizovali v kroku 1.)

Pro zálohy po prvním než nemusíte vytvořit nový kontejner úložiště. Zvažte však vygenerování nového šifrovacího klíče pokaždé, když provedete zálohování, abyste měli jistotu, že máte aktuální klíč uložený na místě, které si zapamatujete.

### <a name="access-a-backup-data-source-during-an-outage"></a>Přístup k záložnímu zdroji dat během výpadku

Pokud chcete získat přístup k kontejneru zálohování z avere vFXT pro cluster Azure, postupujte podle tohoto postupu:

1. V případě potřeby vytvořte nový avere vFXT pro cluster Azure v neovlivněné oblasti.

   > [!TIP]
   > Když vytvoříte avere vFXT pro cluster Azure, můžete uložit kopii jeho šablony a parametrů pro jeho vytvoření. Pokud tyto informace při vytváření primárního clusteru uložíte, můžete je použít k vytvoření náhradního clusteru se stejnými vlastnostmi. Na stránce [Souhrn](avere-vfxt-deploy.md#validation-and-purchase) klikněte na odkaz **Stáhnout šablonu a parametry** . Před vytvořením clusteru tyto informace uložte do souboru.

1. Přidejte nový souborového Cloud Core, který odkazuje na duplicitní kontejner objektů BLOB.

   Nezapomeňte určit, že cílový kontejner již obsahuje data v nastavení **obsahu intervalu** Průvodce vytvořením základního souborového. (Systém vás upozorní, když omylem necháte tuto sadu **prázdnou**.)  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. V případě potřeby aktualizujte klienty tak, aby připojovat nový cluster nebo nový základní souborového místo původního. (Pokud přidáte náhradní jádro souborového se stejným názvem a cestou spojení jako původní kontejner, nebudete muset aktualizovat klientské procesy, pokud nepotřebujete připojit nový cluster na novou IP adresu.)

## <a name="next-steps"></a>Další kroky

* Další informace o přizpůsobení nastavení pro avere vFXT pro Azure najdete v tématu [ladění clusteru](avere-vfxt-tuning.md).
* Další informace o zotavení po havárii a vytváření odolných aplikací v Azure:

  * [Technické pokyny k odolnosti Azure](/azure/architecture/framework/resiliency/overview)
  * [Zotavení z přerušení služeb na úrovni celé oblasti](/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Zotavení po havárii a vysoká dostupnost pro aplikace Azure](/azure/architecture/framework/resiliency/backup-and-recovery)
  <!-- can't find these in the source tree to use relative links -->