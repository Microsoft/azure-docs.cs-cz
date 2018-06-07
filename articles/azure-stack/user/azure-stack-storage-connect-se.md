---
title: Připojení k předplatné Azure zásobníku nebo účet úložiště storage Exploreru | Microsoft Docs
description: Zjistěte, jak se připojit k předplatnému Azure zásobníku Průzkumníka úložiště
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 9704f05cc6da97e33c0043b93acedc9e66bdcc36
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714897"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Připojení k předplatné Azure zásobníku nebo účet úložiště storage Exploreru

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

V tomto článku se dozvíte, jak se připojit k Azure zásobníku předplatných a účtů úložiště pomocí Průzkumníka úložiště. Azure storage Exploreru je samostatná aplikace, která umožňuje snadno pracovat s daty Azure zásobníku úložiště v systému Windows, systému macOS a Linux.

> [!NOTE]  
> Je několik nástrojů, které jsou k dispozici pro přesun dat do a z Azure zásobníku úložiště. Další informace najdete v tématu [přenos dat nástrojů pro úložiště Azure zásobníku](azure-stack-storage-transfer.md).

Pokud jste si ještě nenainstalovali storage Exploreru [stáhnout storage Exploreru](http://www.storageexplorer.com/) a nainstalujte ji.

Po připojení k předplatnému Azure zásobníku nebo účet úložiště, můžete použít [Azure storage explorer články](../../vs-azure-tools-storage-manage-with-storage-explorer.md) pro práci s daty Azure zásobníku. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Příprava pro připojení k Azure zásobníku

Musíte přímý přístup k Azure zásobník nebo připojení k síti VPN pro Průzkumníka úložiště přístup k předplatnému Azure zásobníku. Další informace o tom, jak nastavit připojení VPN ke službě Azure Stack, najdete v tématu [Připojení ke službě Azure Stack pomocí sítě VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

Pro Azure Development Kit zásobníku musíte exportovat certifikát od kořenové autority zásobník Azure.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Exportovat a importovat certifikát Azure zásobníku

1. Otevřete `mmc.exe` na hostitelském počítači služby Azure zásobníku nebo místního počítače pomocí připojení VPN do protokolů Azure. 

2. V **soubor**, vyberte **přidat nebo odebrat modul Snap-in**a poté přidejte **certifikáty** ke správě **uživatelským účtem**.

3. V části **Root\Certificated konzoly (místní počítač) \Trusted Root Certification Authorities\Certificates** najít **AzureStackSelfSignedRootCert**.

    ![Načtení kořenového certifikátu služby Azure Stack pomocí mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

4. Pravým tlačítkem na certifikát, vyberte **všechny úlohy** > **exportovat**a pak postupujte podle pokynů pro export certifikátu s **X.509 s kódováním Base-64 (. CER)**.

    Vyexportovaný certifikát se použije v dalším kroku.

5. Otevřete storage explorer, a pokud se zobrazí **připojit k Azure Storage** dialogové okno pole, zrušte ji.

6. Na **upravit** nabídky, přejděte na příkaz **certifikáty SSL**a potom vyberte **importu certifikátů**. Pomocí dialogového okna pro výběr souborů najděte a otevřete certifikát, který jste exportovali v předchozím kroku.

    Po importu certifikátu, se zobrazí výzva k restartování Průzkumníka úložiště.

    ![Import certifikátu do Průzkumníka úložiště](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

7. Po restartování Průzkumník úložišť, vyberte **upravit** nabídky a zkontrolujte, zda **zásobník Azure cíl** je vybrána. Pokud tomu tak není, vyberte **zásobník Azure cíl**a pak znovu spusťte Průzkumníka úložiště pro tato změna se projeví. Tato konfigurace je nutná pro kompatibilitu s vaším prostředím Azure Stack.

    ![Kontrola, že položka Target Azure Stack je vybraná](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription"></a>Připojení k předplatnému Azure Stack

Pomocí následujících kroků pro připojení k předplatnému Azure zásobníku Průzkumníka úložiště.

1. V levém podokně storage Exploreru vyberte **Správa účtů**. 
    Zobrazí se všechny předplatného společnosti Microsoft, který jste přihlášení.

2. Chcete-li se připojit k předplatnému Azure zásobníku, vyberte **přidat účet**.

    ![Přidání účtu Azure Stack](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. V okně připojení k Azure Storage dialogové okno v části **prostředí Azure**, vyberte **Azure** nebo **Azure China**, která závisí na Azure zásobníku účet, který se používá, vyberte **Přihlášení** se přihlásit pomocí účtu Azure zásobníku přidružené minimálně jedno aktivní předplatné Azure zásobníku.

    ![Připojení k úložišti Azure](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Po úspěšném přihlášení pomocí účtu Azure Stack se v levém podokně zobrazí předplatná Azure Stack přidružená k tomuto účtu. Vyberte předplatná Azure Stack, se kterými chcete pracovat, a pak vyberte **Apply** (Použít). (Zaškrtnutím nebo zrušením zaškrtnutí políčka **All subscriptions** (Všechna předplatná) přepínáte výběr všech nebo žádných z uvedených předplatných Azure Stack.)

    ![Výběr předplatných Azure Stack po vyplnění dialogového okna Custom Cloud Environment](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    V levém podokně se zobrazí všechny účty úložišť přidružené k vybraným předplatným Azure Stack.

    ![Seznam účtů úložiště, včetně účtů předplatného Azure Stack](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Připojit k účtu úložiště Azure zásobníku

Také můžete připojit k účtu úložiště Azure zásobníku pomocí názvu účtu úložiště a pár klíčů.

1. V levém podokně storage Exploreru vyberte Správa účtů. Zobrazí se všechny účty Microsoft, které jste přihlášení.

    ![Přidání účtu](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Chcete-li se připojit k předplatnému Azure zásobníku, vyberte **přidat účet**.

    ![Přidání účtu](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. V okně připojení k Azure Storage, dialogové okno Vybrat **použít název účtu úložiště a klíč**.

4. Zadejte název účtu v **název účtu**, vložte klíč účtu do **klíč účtu** textového pole, vyberte **jiné (zadejte níže)** v **koncové body úložiště domény** a vstupní koncový bod Azure zásobníku.

    Koncový bod Azure Stack zahrnuje dvě části: název, oblast a doména Azure zásobníku. V sadě Azure zásobníku Development Kit je výchozí koncový bod **local.azurestack.external**. Pokud si nejste jistí o váš koncový bod, obraťte se na správce cloudu.

    ![Připojení, název a klíč](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Vyberte **Connect** (Připojit).
6. Po úspěšném připojení účtu úložiště účet úložiště zobrazí s (**externí, ostatní**) připojí k jeho názvu.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Další postup

* [Začínáme se storage Explorerem](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Úložiště Azure zásobník: rozdíly a důležité informace](azure-stack-acs-differences.md)
* Další informace o Azure storage najdete v tématu [Úvod do Microsoft Azure storage](../../storage/common/storage-introduction.md)
