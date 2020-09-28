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
ms.openlocfilehash: c111752dcca03781dfb2a11fb100dafc4bad90f7
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91401038"
---
## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Trezor služby Recovery Services je správní entitou, která ukládá body obnovení vytvořené v průběhu času a nabízí rozhraní, pomocí kterého je možné provádět operace související se zálohováním. Mezi ně patří zálohování na vyžádání, provádění obnovení a vytváření zásad zálohování.

Pokud chcete vytvořit trezor služby Recovery Services, postupujte podle těchto kroků.

1. Přihlaste se ke svému předplatnému na webu [Azure Portal](https://portal.azure.com/).

1. V nabídce vlevo vyberte **Všechny služby**.

    ![Vyberte Všechny služby.](./media/backup-create-rs-vault/click-all-services.png)

1. V dialogovém okně **Všechny služby** zadejte *Recovery Services*. Seznam prostředků se filtruje podle vašeho zadání. Ze seznamu prostředků vyberte **Trezory služby Recovery Services**.

    ![Zadání a volba trezorů služby Recovery Services](./media/backup-create-rs-vault/all-services.png)

    Zobrazí se seznam trezorů služby Recovery Services v předplatném.

1. Na řídicím panelu **Trezory služby Recovery Services** vyberte **Přidat**.

    ![Přidání trezoru služby Recovery Services](./media/backup-create-rs-vault/add-button-create-vault.png)

    Otevře se dialogové okno **Trezor služby Recovery Services**. Zadejte hodnoty položek **Název**, **Předplatné**, **Skupina prostředků** a **Umístění**.

    ![Konfigurace trezoru služby Recovery Services](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Name** (Název): Zadejte popisný název pro identifikaci trezoru. Název musí být v rámci předplatného Azure jedinečný. Zadejte název, který má délku aspoň 2 znaky, ale ne víc než 50 znaků. Název musí začínat písmenem a může obsahovat pouze písmena, číslice a pomlčky.
   - **Předplatné:** Vyberte předplatné, které chcete použít. Pokud jste členem jenom jednoho předplatného, název se zobrazí. Pokud si nejste jisti, které předplatné použít, použijte výchozí (navrhované) předplatné. Více možností je dostupných, jen pokud je váš pracovní nebo školní účet přidružený k více předplatným Azure.
   - **Skupina prostředků:** Použijte existující skupinu prostředků, nebo vytvořte novou. Pokud chcete zobrazit seznam dostupných skupin prostředků ve vašem předplatném, vyberte **Použít existující** a pak v rozevíracím seznamu vyberte prostředek. Pokud chcete vytvořit novou skupinu prostředků, vyberte **Vytvořit novou** a zadejte název. Další informace o skupinách prostředků najdete v tématu [Přehled Azure Resource Manageru](../articles/azure-resource-manager/management/overview.md).
   - **Location** (Umístění): Vyberte zeměpisnou oblast trezoru. Pokud chcete vytvořit trezor pro ochranu zdroje dat, trezor *musí* být ve stejné oblasti jako zdroj dat.

      > [!IMPORTANT]
      > Pokud si nejste jisti umístěním zdroje dat, dialogové okno zavřete. V portálu přejděte na seznam vašich prostředků. Pokud máte zdroje dat v několika oblastech, vytvořte trezor služby Recovery Services pro každou z nich. Vytvořte trezor v prvním umístění předtím, než vytvoříte trezor pro jiné umístění. Není nutné zadávat účty úložiště pro ukládání zálohovaných dat. Trezor služby Recovery Services a služba Azure Backup to zvládnou automaticky.
      >
      >

1. Po zadání hodnot vyberte **Zkontrolovat a vytvořit**.

    ![Snímek obrazovky, který zobrazuje tlačítko pro revizi + vytvoření v procesu vytvoření Recovery Servicesového trezoru.](./media/backup-create-rs-vault/review-and-create.png)

1. Až budete připraveni vytvořit trezor služby Recovery Services, vyberte **Vytvořit**.

    ![Vytvoření trezoru služby Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    Vytvoření trezoru služby Recovery Services může chvíli trvat. Sledujte oznámení o stavu v oblasti **Oznámení** v pravém horním rohu portálu. Po vytvoření se trezor zobrazí v seznamu trezorů služby Recovery Services. Pokud trezor nevidíte, vyberte **Aktualizovat**.

     ![Aktualizace seznamu trezorů záloh](./media/backup-create-rs-vault/refresh-button.png)

>[!IMPORTANT]
> Důrazně doporučujeme, abyste před konfigurací záloh v trezoru zkontrolovali **Typ replikace úložiště** a **Nastavení zabezpečení**. Další informace najdete v části [Nastavení redundance úložiště](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy).
