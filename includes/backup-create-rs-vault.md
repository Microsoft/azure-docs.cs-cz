---
title: zahrnout soubor
description: zahrnout soubor
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: 6167774171affda7e5469d5852a79657a6da700d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262665"
---
## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Trezor služby Recovery Services je entita úložiště, která ukládá body obnovení vytvořené v průběhu času. Obsahuje také zásady zálohování, které jsou přidruženy k chráněným položkám.

Chcete-li vytvořit trezor služby Recovery Services, postupujte takto.

1. Přihlaste se ke svému předplatnému na [webu Azure Portal](https://portal.azure.com/).

1. V levé nabídce vyberte **Všechny služby**.

    ![Vybrat všechny služby](./media/backup-create-rs-vault/click-all-services.png)

1. V dialogovém okně **Všechny služby** zadejte *služby obnovení*. Seznam zdrojů filtruje podle vašeho vstupu. V seznamu zdrojů vyberte **trezory služby Recovery Services**.

    ![Zadání a výběr trezorů služby Recovery Services](./media/backup-create-rs-vault/all-services.png)

    Zobrazí se seznam trezorů služby Recovery Services v předplatném.

1. Na řídicím panelu **trezorů služby Recovery Services** vyberte **Přidat**.

    ![Přidání trezoru služby Recovery Services](./media/backup-create-rs-vault/add-button-create-vault.png)

    Otevře se dialogové okno **trezoru služby Recovery Services.** Zadejte hodnoty pro **název**, **odběr**, **skupinu prostředků**a **umístění**.

    ![Konfigurace trezoru služby Recovery Services](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Název**: Zadejte popisný název pro identifikaci trezoru. Název musí být jedinečný pro předplatné Azure. Zadejte název, který má alespoň 2, ale ne více než 50 znaků. Název musí začínat písmenem a skládat pouze z písmen, čísel a spojovníků.
   - **Předplatné**: Zvolte předplatné, které chcete použít. Pokud jste členem pouze jednoho předplatného, uvidíte toto jméno. Pokud si nejste jistí, které předplatné použít, použijte výchozí (navrhované) předplatné. Existuje několik možností pouze v případě, že váš pracovní nebo školní účet je přidružený k více než jednomu předplatnému Azure.
   - **Skupina prostředků**: Použijte existující skupinu prostředků nebo vytvořte novou. Pokud chcete zobrazit seznam dostupných skupin prostředků v předplatném, vyberte **Použít existující**a vyberte prostředek z rozevíracího seznamu. Chcete-li vytvořit novou skupinu prostředků, vyberte **Vytvořit nový** a zadejte název. Další informace o skupinách prostředků najdete [v tématu Přehled Správce prostředků Azure](../articles/azure-resource-manager/management/overview.md).
   - **Umístění**: Vyberte zeměpisnou oblast pro úschovnu. Chcete-li vytvořit trezor pro ochranu virtuálních počítačů, *musí* být trezor ve stejné oblasti jako virtuální počítače.

      > [!IMPORTANT]
      > Pokud si nejste jistí umístěním virtuálního počítače, zavřete dialogové okno. Přejděte na seznam virtuálních počítačů na portálu. Pokud máte virtuální počítače v několika oblastech, vytvořte trezor služby Recovery Services v každé oblasti. Před vytvořením úschovny pro jiné umístění vytvořte úschovnu na prvním místě. Není nutné zadávat účty úložiště pro ukládání záložních dat. Trezor služby Recovery Services a azure backup to automaticky zvládnou.
      >
      >

1. Až budete připraveni vytvořit trezor Služby pro obnovení, vyberte **Vytvořit**.

    ![Vytvoření trezoru služby Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    Vytvoření trezoru služby Recovery Services může chvíli trvat. Sledujte oznámení o stavu v oblasti **Oznámení** v pravém horním rohu portálu. Po vytvoření trezoru je zobrazen v seznamu trezorů služby Recovery Services. Pokud trezor nevidíte, vyberte **Aktualizovat**.

     ![Aktualizace seznamu záložních trezorů](./media/backup-create-rs-vault/refresh-button.png)
