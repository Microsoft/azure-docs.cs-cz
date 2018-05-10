---
title: Používat pro přístup k Azure Resource Manager MSI virtuálních počítačů Linux
description: Kurz vás provede procesem pomocí Linux virtuálního počítače spravované služby Identity (MSI) pro přístup k Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: ec932451d82e891b2c9b11d91f44d4dc5fce6828
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-resource-manager"></a>Používat pro přístup k Azure Resource Manager Linux virtuálního počítače spravované služby Identity (MSI)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak povolit identita spravované služby (MSI) pro virtuální počítač s Linuxem, a potom tuto identitu používat pro přístup k rozhraní API služby Azure Resource Manager. Identita spravované služby je automaticky prováděna nástrojem Azure a umožňují ověření pro služby, které podporují ověřování Azure AD bez nutnosti vložení přihlašovací údaje do vašeho kódu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Povolit MSI na virtuální počítač s Linuxem 
> * Udělit přístup virtuálních počítačů do skupiny prostředků ve službě Správce prostředků Azure 
> * Získání přístupového tokenu pomocí identity virtuálního počítače a použít jej k vyvolání Azure Resource Manager 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Vytvořit virtuální počítač s Linuxem do nové skupiny prostředků

V tomto kurzu vytvoříme nový virtuální počítač s Linuxem. Můžete také povolit MSI na existující virtuální počítač.

1. Klikněte na tlačítko **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Vyberte **Compute** a potom vyberte **Ubuntu Server 16.04 LTS**.
3. Zadejte informace o virtuálním počítači. Pro **typ ověřování**, vyberte **veřejný klíč SSH** nebo **heslo**. Vytvořené pověření umožňují přihlášení k virtuálnímu počítači.

    ![Obrázek alternativní text](../media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Vyberte **předplatné** pro virtuální počítač v rozevírací nabídce.
5. Chcete-li vybrat nový **skupiny prostředků** chcete virtuální počítač lze vytvořit v, zvolte **vytvořit nový**. Jakmile budete hotovi, klikněte na **OK**.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **zobrazit všechny** nebo změňte typ filtru disku podporované. V okně Nastavení ponechte výchozí nastavení a klikněte na **OK**.

## <a name="enable-msi-on-your-vm"></a>Povolit MSI na vašem virtuálním počítači

Virtuální počítač MSI umožňuje získat přístupové tokeny z Azure AD, aniž by bylo třeba uvést přihlašovací údaje do vašeho kódu. Povolení spravovat Identity služby na virtuálním počítači, nemá dvě věci: zaregistruje virtuální počítač s Azure Active Directory k vytvoření jeho spravovanou identitu a nakonfiguruje identitu ve virtuálním počítači.

1. Vyberte **virtuálního počítače** , které chcete povolit MSI v.
2. V levém navigačním panelu klikněte na tlačítko **konfigurace**.
3. Zobrazí **identita spravované služby**. Registrovat a povolit soubor MSI, vyberte **Ano**, pokud chcete zakázat, vyberte Ne.
4. Ujistěte se, kliknete na tlačítko **Uložit** konfiguraci uložíte.

    ![Obrázek alternativní text](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-azure-resource-manager"></a>Udělit přístup virtuálních počítačů do skupiny prostředků ve službě Správce prostředků Azure 

Pomocí Instalační služby MSI kódu můžete získat přístupové tokeny k ověřování k prostředkům, které podporují ověřování Azure AD. Rozhraní API služby Azure Resource Manager podporuje ověřování Azure AD. Nejprve musíme udělení tohoto Virtuálního počítače identitu přístup k prostředku v Azure Resource Manager, v tomto případě skupině prostředků, ve kterém se nachází virtuální počítač.  

1. Přejděte na kartu pro **skupiny prostředků**.
2. Vyberte konkrétní **skupiny prostředků** jste vytvořili dříve.
3. Přejděte na **přístup control(IAM)** na levém panelu.
4. Chcete-li **přidat** nové přiřazení role pro virtuální počítač. Zvolte **Role** jako **čtečky**.
5. V dalším rozevíracím **přiřadit přístup** prostředek **virtuální počítač**.
6. Dále zkontrolujte správné předplatné, je uvedena ve **předplatné** rozevíracího seznamu. A pro **skupiny prostředků**, vyberte **všechny skupiny zdrojů**.
7. Nakonec v **vyberte** vyberte virtuální počítač Linux rozevíracího seznamu a klikněte na **Uložit**.

    ![Obrázek alternativní text](../media/msi-tutorial-linux-vm-access-arm/msi-permission-linux.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Získání přístupového tokenu pomocí identity Virtuálního počítače a použít jej k vyvolání Resource Manager 

K dokončení těchto kroků budete potřebovat klientem SSH. Pokud používáte systém Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about). Pokud potřebujete pomoc konfigurace klíče klient SSH, přečtěte si téma [jak klíče použití SSH se systémem Windows v Azure](../../virtual-machines/linux/ssh-from-windows.md), nebo [jak vytvořit a používat SSH pár veřejného a privátního klíče pro virtuální počítače s Linuxem v Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Na portálu, přejděte k virtuálním počítačům s Linuxem a v **přehled**, klikněte na tlačítko **Connect**.  
2. **Připojit** na virtuální počítač s klientem SSH podle svého výběru. 
3. V okně terminálu pomocí CURL, vytvořte žádost na místní koncový bod MSI se získat přístupový token pro Azure Resource Manager.  
 
    Níže je CURL požadavek na přístupový token.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
    
    > [!NOTE]
    > Hodnota parametru "prostředek" musí být přesná shoda pro očekávané službou Azure AD.  V případě ID prostředku Resource Manager je nutné zahrnout do adresy koncové lomítko, v identifikátoru URI. 
    
    Odpověď obsahuje přístupový token, potřebujete získat přístup k Azure Resource Manager. 
    
    Odpověď:  

    ```bash
    {"access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
    ```
    
    Tento token přístupu můžete použít pro přístup k Azure Resource Manager, např. Chcete-li zobrazit podrobné informace o skupině prostředků, do které jste dříve udělen přístup tento virtuálních počítačů. Nahraďte hodnoty \<ID PŘEDPLATNÉHO\>, \<skupiny prostředků\>, a \<TOKENU přístupu\> s těmi, které jste vytvořili dříve. 
    
    > [!NOTE]
    > Adresa URL je malá a velká písmena, zajistěte proto, pokud používáte přesný případ stejné, jako jste použili dříve, když jste s názvem skupiny prostředků a velká písmena "G" v "resourceGroup".  
    
    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Odpověď zpět s konkrétní informace o skupinách prostředků: 
     
    ```bash
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}} 
    ```
     
## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](overview.md). 

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.

