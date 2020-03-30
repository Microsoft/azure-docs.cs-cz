---
title: Pokyny pro zotavení po havárii pro Avere vFXT pro Azure
description: Jak chránit data v Avere vFXT pro Azure před náhodným odstraněním nebo výpadky
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 28278f76497d6e9d0fee221bb4ef32fe6d369db0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966646"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Pokyny pro zotavení po havárii pro Avere vFXT pro Azure

Tento článek popisuje strategie pro ochranu pracovního postupu Avere vFXT pro Azure a poskytuje pokyny pro zálohování dat, abyste se mohli zotavit z nehod nebo výpadků.

Avere vFXT pro Azure dočasně ukládá data do mezipaměti. Data se ukládají dlouhodobě v back-endových úložných systémech – místních hardwarových systémech, kontejnerech úložiště objektů blob Azure nebo obojí.

Chcete-li se chránit před výpadky a možnou ztrátou dat, zvažte tyto čtyři oblasti:

* Ochrana proti prostojům, pokud systém Avere vFXT pro Azure přestane být k dispozici
* Ochrana dat v mezipaměti clusteru
* Ochrana dat v back-endovém hardwarovém úložišti NAS
* Ochrana dat v back-endovém cloudovém úložišti Azure Blob

Každý zákazník Avere vFXT pro Azure musí vytvořit vlastní komplexní plán zotavení po havárii, který zahrnuje plány pro tyto položky. Můžete také sestavit odolnost proti chybám do aplikací, které používáte s clusteru vFXT. Přečtěte si odkazy v [dalších krocích](#next-steps) pro nápovědu.

## <a name="protect-against-downtime"></a>Ochrana před prostojemi

Redundance je integrovaná v produktu Avere vFXT for Azure:

* Cluster je vysoce dostupný a jednotlivé uzly clusteru mohou přepojit převzetí služeb při selhání s minimálním přerušením.
* Data změněná v mezipaměti se pravidelně zapisují do back-endových základních filerů (hardwarový NAS nebo Objekt blob Azure) pro dlouhodobé úložiště.

Každý avere vFXT pro cluster Azure musí být umístěn v jedné zóně dostupnosti, ale můžete použít redundantní clustery umístěné v různých zónách nebo různých oblastech k rychlému poskytnutí přístupu v případě regionálního výpadku.

Kontejnery úložiště můžete také umístit do více oblastí, pokud máte obavy o ztrátu přístupu k datům. Mějte však na paměti, že transakce mezi oblastmi mají vyšší latenci a vyšší náklady než transakce, které zůstávají v rámci oblasti.

## <a name="protect-data-in-the-cluster-cache"></a>Ochrana dat v mezipaměti clusteru

Data uložená v mezipaměti jsou vždy zapsána do základních filerů před pravidelným vypnutím, ale při nekontrolovaném vypnutí mohou být změněná data v mezipaměti ztracena.

Pokud cluster použijete pouze k optimalizaci čtení souborů, nebudou žádné změny, které by bylo možné ztratit. Pokud cluster používáte také k ukládání změn (zápisů) do mezipaměti, zvažte, zda chcete upravit [zásady mezipaměti](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html) základních filers.<!-- link to legacy doc --> přizpůsobení, jak často jsou data zapisována do dlouhodobého úložiště.

Obecně platí, že plán obnovení by se měl zaměřit na zálohování back-endových úložných systémů, které uchovávají více dat a obvykle jsou důležitější pro obnovení pracovního postupu po selhání.

## <a name="protect-data-in-nas-core-filers"></a>Ochrana dat v základních filery NAS

Pomocí přijatých metod můžete chránit data uložená v místním hardwarovém fileru NAS, včetně snímků a úplných záloh podle doporučení poskytovatele NAS. Zotavení po havárii pro tyto základní filers je nad rámec tohoto článku.

## <a name="protect-data-in-azure-blob-storage"></a>Ochrana dat v úložišti objektů blob Azure

Avere vFXT pro Azure používá místně redundantní úložiště (LRS) pro základní filery objektů blob Azure. To znamená, že data v kontejnerech objektů Blob se automaticky zkopírují pro ochranu proti přechodným selháním hardwaru v rámci datového centra.

Tato část obsahuje tipy, jak dále chránit data v úložišti objektů Blob před výpadky v celé oblasti nebo náhodným odstraněním.

Mezi osvědčené postupy pro ochranu dat v úložišti objektů blob Azure patří:

* Často kopírujte důležitá data do jiného účtu úložiště v jiné oblasti (tak často, jak je určeno plánem zotavení po havárii).
* Řízení přístupu k datům ve všech cílových systémech, aby se zabránilo náhodnému odstranění nebo poškození. Zvažte použití [uzamčení prostředků](../azure-resource-manager/management/lock-resources.md) v úložišti dat.
* Povolte funkci [snímek cloudu](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) Avere vFXT for Azure pro vaše filery jádra objektu Blob.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Kopírování základních dat fileru Avere vFXT do záložního účtu

Následujícím postupem vytvořte zálohu dat v jiném účtu.

1. V případě potřeby vygenerujte nový šifrovací klíč a bezpečně jej uložte mimo ohrožené systémy.

   Pokud jsou vaše data šifrována clusterem Avere vFXT pro Azure, měli byste před zkopírováním dat do jiného účtu úložiště vygenerovat nový šifrovací klíč. Uložte tento klíč a heslo bezpečně v zařízení, které je bezpečné a které nebude ovlivněno regionální selhání.

   Tento klíč je nutné zadat při přidávání kontejneru do clusteru – i v případě, že jej znovu přidáváte do původního clusteru.

   Čtení [nastavení šifrování cloudu](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> podrobné informace.

   Pokud váš kontejner používá jenom integrované šifrování Azure, můžete tento krok přeskočit.

1. Odeberte základní filer ze systému. To vynutí clusteru zapsat všechna změněná data do úložiště back-end.

   I když budete muset znovu přidat základní filer po zálohování, odstranění je nejlepší způsob, jak zaručit, že všechna data jsou zcela zapsána do back-endu. (Možnost "pozastavit" může někdy ponechat změněná data v mezipaměti.) <!-- xxx true? or just metadata? -->

   Poznamenejte si název základního fileru a informace o spojení (uvedené na stránce **Obor názvů** v ovládacím panelu), abyste je mohli replikovat při opětovném přidání kontejneru po zálohování.

   Pomocí ovládacího panelu clusteru odeberte základní filer. [Otevřete ovládací panel clusteru](avere-vfxt-cluster-gui.md) a zvolte **Core filer** > **Manage core filers**. Najděte systém úložiště, který chcete zálohovat, a pomocí tlačítka **Odebrat** jej odstraňte z clusteru.

1. Vytvořte nový prázdný kontejner úložiště objektů Blob v jiném účtu úložiště v jiné oblasti.

1. Ke kopírování dat v základním fileru do nového kontejneru použijte libovolný vhodný nástroj pro kopírování. Kopie musí replikovat data beze změn a bez narušení proprietární hospodařící formát souborového systému používaný Avere vFXT pro Azure. Mezi nástroje založené na Azure patří [AzCopy](../storage/common/storage-use-azcopy-v10.md), [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)a [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

1. Po zkopírování dat do záložního kontejneru přidejte původní kontejner zpět do clusteru, jak je popsáno v [části Konfigurace úložiště](avere-vfxt-add-storage.md).

   * Použijte stejný název základního souboru a informace o spojení, aby se pracovní postupy klienta nemusely měnit.
   * Nastavte hodnotu **obsahu bloku** na možnost existujících dat.
   * Pokud byl kontejner zašifrován clusterem, je nutné zadat aktuální šifrovací klíč pro jeho obsah. (Toto je klíč, který jste aktualizovali v kroku jedna.)

Pro zálohy po první, není nutné vytvářet nový kontejner úložiště. Zvažte však generování nového šifrovacího klíče pokaždé, když provedete zálohu, abyste se ujistili, že máte aktuální klíč uložený na místě, které si pamatujete.

### <a name="access-a-backup-data-source-during-an-outage"></a>Přístup k záložnímu zdroji dat během výpadku

Chcete-li získat přístup ke kontejneru zálohování z clusteru Avere vFXT pro Azure, postupujte podle tohoto postupu:

1. V případě potřeby vytvořte nový Avere vFXT pro cluster Azure v neovlivněné oblasti.

   > [!TIP]
   > Když vytvoříte Avere vFXT pro cluster Azure, můžete uložit kopii jeho šablony vytvoření a parametrů. Pokud tyto informace uložíte při vytváření primárního clusteru, můžete je použít k vytvoření náhradního clusteru se stejnými vlastnostmi. Na [stránce souhrnu](avere-vfxt-deploy.md#validation-and-purchase) klikněte na odkaz **Stáhnout šablonu a parametry.** Před vytvořením clusteru uložte informace do souboru.

1. Přidejte nový filer cloudového jádra, který odkazuje na duplicitní kontejner objektů Blob.

   Ujistěte se, že určit, že cílový kontejner již obsahuje data v nastavení **obsahu bloku** základního filer vytvoření průvodce. (Systém by vás měl upozornit, pokud omylem necháte tuto sadu **prázdné**.)  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. V případě potřeby aktualizujte klienty tak, aby namísto původního připojení nového clusteru nebo nového základního fileru. (Pokud přidáte náhradní základní filer se stejným názvem a spojovací cestou jako původní kontejner, nebudete muset aktualizovat klientské procesy, pokud nebudete muset připojit nový cluster na novou adresu IP.)

## <a name="next-steps"></a>Další kroky

* Další informace o přizpůsobení nastavení Pro Avere vFXT pro Azure najdete v části [Optimalizace clusteru](avere-vfxt-tuning.md).
* Další informace o zotavení po havárii a vytváření odolných aplikací v Azure:

  * [Technické pokyny k odolnosti Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
  * [Zotavení z přerušení služeb na úrovni celé oblasti](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Zotavení po havárii a vysoká dostupnost pro aplikace Azure](<https://docs.microsoft.com/azure/resiliency/resiliency-disaster-recovery-high-availability-azure-applications>)
  <!-- can't find these in the source tree to use relative links -->
