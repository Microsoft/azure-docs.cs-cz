---
title: Připojení Průzkumníka služby storage k předplatnému Azure Stack nebo účtu úložiště | Dokumentace Microsoftu
description: Informace o připojení storage Exploreru k předplatnému Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/10/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: ab498a108eba39088aa610188918934dd4ec1d3e
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078949"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Připojení Průzkumníka služby storage k předplatnému Azure Stack nebo účet úložiště

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

V tomto článku se dozvíte, jak se připojit ke svým předplatným Azure Stack a účty úložiště pomocí Průzkumníka služby storage. Průzkumník služby Azure storage je samostatná aplikace, která umožňuje jednoduchá práci s daty Azure Stack úložiště ve Windows, macOS a Linuxu.

> [!NOTE]  
> Existuje několik nástrojů, které jsou k dispozici pro přesun dat do a z úložiště služby Azure Stack. Další informace najdete v tématu [nástroje pro Azure Stack úložiště pro přenos dat](azure-stack-storage-transfer.md).

Pokud jste nenainstalovali Průzkumníka služby storage, [stáhněte si Průzkumníka služby storage](http://www.storageexplorer.com/) a nainstalujte ho.

Po připojení k předplatnému Azure Stack nebo účet úložiště, můžete použít [služby Azure storage explorer články](../../vs-azure-tools-storage-manage-with-storage-explorer.md) pro práci s daty Azure Stack. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Příprava pro připojení ke službě Azure Stack

Potřebujete přímý přístup ke službě Azure Stack nebo připojení k síti VPN pro Průzkumníka služby storage pro přístup k předplatnému Azure Stack. Další informace o tom, jak nastavit připojení VPN ke službě Azure Stack, najdete v tématu [Připojení ke službě Azure Stack pomocí sítě VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

Pro Azure Stack Development Kit je potřeba vyexportovat kořenový certifikát autority Azure stacku.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Exportovat a importovat certifikát služby Azure Stack

1. Otevřít `mmc.exe` na hostitelském počítači Azure Stack nebo místním počítači pomocí připojení VPN ke službě Azure Stack. 

2. V **souboru**vyberte **Přidat/odebrat modul Snap-in**a pak přidejte **certifikáty** ke správě **Můj uživatelský účet**.

3. V části **Console Root\Certificated (Local Computer) \Trusted Root Certification Authorities\Certificates** najít **AzureStackSelfSignedRootCert**.

    ![Načtení kořenového certifikátu služby Azure Stack pomocí mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

4. Klikněte pravým tlačítkem na certifikát, vyberte **všechny úkoly** > **exportovat**a pak postupujte podle pokynů a vyexportujte certifikát s **kódování Base-64 X.509 (. CER)**.

    Vyexportovaný certifikát se použije v dalším kroku.

5. Otevřete storage explorer, a pokud se zobrazí **připojit ke službě Azure Storage** dialogovém okně ho zrušit.

6. Na **upravit** nabídky, přejděte k **certifikáty SSL**a pak vyberte **importovat certifikáty**. Pomocí dialogového okna pro výběr souborů najděte a otevřete certifikát, který jste exportovali v předchozím kroku.

    Po importu certifikátu, budete vyzváni k restartování storage Exploreru.

    ![Importujte certifikát do Průzkumníka služby storage](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

7. Po restartování storage Exploreru vyberte **upravit** nabídky a zkontrolujte, jestli **Target Azure Stack** zaškrtnuto. Pokud tomu tak není, vyberte **Target Azure Stack**a restartujte storage explorer, aby tato změna projevila. Tato konfigurace je nutná pro kompatibilitu s vaším prostředím Azure Stack.

    ![Kontrola, že položka Target Azure Stack je vybraná](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription-with-azure-ad"></a>Připojte se k předplatnému Azure Stack s Azure AD

Pomocí následujících kroků pro připojení storage Exploreru k předplatnému Azure Stack, který patří k účtu služby Azure Active Directory (Azure AD).

1. V levém podokně storage Exploreru vyberte **spravovat účty**. 
    Zobrazí se všechny odběr služby Microsoft, který jste se přihlásili.

2. Pro připojení k předplatnému Azure Stack, vyberte **přidat účet**.

    ![Přidání účtu Azure Stack](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. V okně připojení k Azure Storage dialogovém okně v části **prostředí Azure**vyberte **Azure** nebo **Azure China**, která závisí na účtu Azure Stack, který se používá, vyberte **Přihlášení** se přihlásit pomocí účtu Azure Stack, který je přidružený alespoň k jednomu aktivnímu předplatnému Azure Stack.

    ![Připojení k úložišti Azure](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Po úspěšném přihlášení pomocí účtu Azure Stack se v levém podokně zobrazí předplatná Azure Stack přidružená k tomuto účtu. Vyberte předplatná Azure Stack, se kterými chcete pracovat, a pak vyberte **Apply** (Použít). (Zaškrtnutím nebo zrušením zaškrtnutí políčka **All subscriptions** (Všechna předplatná) přepínáte výběr všech nebo žádných z uvedených předplatných Azure Stack.)

    ![Výběr předplatných Azure Stack po vyplnění dialogového okna Custom Cloud Environment](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    V levém podokně se zobrazí všechny účty úložišť přidružené k vybraným předplatným Azure Stack.

    ![Seznam účtů úložiště, včetně účtů předplatného Azure Stack](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-subscription-with-ad-fs-account"></a>Připojte se k předplatnému Azure Stack pomocí účtu služby AD FS

> [!Note]  
> Prostředí pro přihlášení k Azure Federated Services (AD FS) podporuje Průzkumník služby Storage 1.2.0 nebo novější verze s Azure Stack 1804 nebo novější aktualizace.
Pomocí následujících kroků pro připojení storage Exploreru k předplatnému Azure Stack, který patří k účtu služby AD FS.

1. Vyberte **spravovat účty**. V Průzkumníku seznam předplatných Microsoft, které jste se přihlásili.
2. Vyberte **přidat účet** pro připojení k předplatnému Azure Stack.

    ![Přidání účtu](media/azure-stack-storage-connect-se/add-an-account.png)

3. Vyberte **Další**. V okně připojení k Azure Storage dialogovém okně v části **prostředí Azure**vyberte **použít vlastní prostředí**, pak klikněte na tlačítko **Další**.

    ![Připojení k Azure Storage](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. Zadejte požadované informace pro vlastní prostředí Azure Stack. 

    | Pole | Poznámky |
    | ---   | ---   |
    | Název prostředí | Pole může uživatel přizpůsobit. |
    | Koncový bod Azure Resource Manageru | Ukázky Azure Resource Manageru prostředků koncové body Azure Stack Development Kit.<br>Pro operátory: https://adminmanagement.local.azurestack.external <br> Pro uživatele: https://management.local.azurestack.external |

    Pokud pracujete ve službě Azure Stack integrovaný systém a nemusíte vědět koncového bodu správy, obraťte se operátor.

    ![Přidání účtu](./media/azure-stack-storage-connect-se/custom-environments.png)

5. Vyberte **přihlášení**, které umožňuje připojení k účtu Azure Stack, který je přidružený alespoň k jednomu aktivnímu předplatnému Azure Stack.



6. Vyberte předplatná Azure Stack, které chcete pracovat. Vyberte **Použít**.

    ![Správa účtů](./media/azure-stack-storage-connect-se/account-management.png)

    V levém podokně se zobrazí všechny účty úložišť přidružené k vybraným předplatným Azure Stack.

    ![Seznam přidružených předplatných](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Připojte se k účtu úložiště Azure Stack

Můžete také připojit k účtu úložiště Azure Stack pomocí názvu účtu úložiště a klíč.

1. V levém podokně storage Exploreru vyberte spravovat účty. Zobrazí se všechny účty Microsoft, které jste se přihlásili.

    ![Přidání účtu](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Pro připojení k předplatnému Azure Stack, vyberte **přidat účet**.

    ![Přidání účtu](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. V okně připojení k Azure Storage, dialogové okno Vybrat **použít název účtu úložiště a klíč**.

4. Zadejte název účtu v **název účtu**, vložte klíč účtu do **klíč účtu** textového pole, vyberte **jiné (zadejte níže)** v **koncových bodů úložiště domény** a vstupní koncový bod služby Azure Stack.

    Koncový bod služby Azure Stack zahrnuje dvě části: název oblasti a doméně služby Azure Stack. V Azure Stack Development Kit, je výchozí koncový bod **local.azurestack.external**. Pokud si nejste jisti o váš koncový bod, obraťte se na správce cloudu.

    ![Připojení, název a klíč](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Vyberte **Connect** (Připojit).
6. Jakmile účet úložiště se úspěšně připojil, účet úložiště zobrazí s (**externí, v jiných**) připojeným k názvu.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Další postup

* [Začínáme se storage Explorerem](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Úložiště Azure Stack: rozdíly a aspekty](azure-stack-acs-differences.md)
* Další informace o službě Azure storage najdete v tématu [seznámení se službou Microsoft Azure storage](../../storage/common/storage-introduction.md)
