---
title: Používat Windows virtuální počítač MSI pro přístup k Azure Key Vault
description: Kurz vás provede procesem použití Windows virtuálního počítače spravované služby Identity (MSI) pro přístup k Azure Key Vault.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: c4570e43c23ff7a23b5d6a97ebd6da0244ed6fb2
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Kurz: Použití Windows virtuálního počítače spravované služby Identity (MSI) pro přístup k Azure Key Vault 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak povolit identita spravované služby (MSI) pro virtuální počítač Windows a pak tuto identitu používat pro přístup k Azure Key Vault. Slouží jako bootstrap, Key Vault umožňuje použít tajný klíč pro přístup k prostředkům, které není zabezpečené pomocí Azure Active Directory (AD), pak klientské aplikaci. Identita spravované služby je automaticky prováděna nástrojem Azure a umožňují ověření pro služby, které podporují ověřování Azure AD, aniž by museli přihlašovací údaje vložit do vašeho kódu. 

Získáte informace o těchto tématech:


> [!div class="checklist"]
> * Povolit identita spravované služby ve virtuálním počítači Windows 
> * Udělit přístup virtuálních počítačů k tajného klíče uložené v Key Vault 
> * Získání přístupového tokenu pomocí identity virtuálního počítače a použít ho k načtení tajný klíč ze Key Vault 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Vytvoření virtuálního počítače s Windows v nové skupině prostředků.

V tomto kurzu vytvoříme nový virtuální počítač s Windows. Můžete také povolit MSI na existující virtuální počítač.

1.  Klikněte na tlačítko **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2.  Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. 
3.  Zadejte informace o virtuálním počítači. **Uživatelské jméno** a **heslo** vytvořený, zde je přihlašovací údaje, které používáte k přihlášení k virtuálnímu počítači.
4.  Vyberte správnou **předplatné** pro virtuální počítač v rozevírací nabídce.
5.  Chcete-li vybrat nový **skupiny prostředků** chcete virtuální počítač lze vytvořit v, zvolte **vytvořit nový**. Jakmile budete hotovi, klikněte na **OK**.
6.  Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. V okně Nastavení ponechte výchozí nastavení a klikněte na **OK**.

    ![Obrázek alternativní text](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Povolit MSI na vašem virtuálním počítači 

Virtuální počítač MSI umožňuje získat přístupové tokeny z Azure AD, aniž by bylo třeba uvést přihlašovací údaje do vašeho kódu. Povolení MSI informuje Azure k vytvoření spravovaného identity pro virtuální počítač. V pozadí, povolení MSI provádí dvě věci: zaregistruje virtuální počítač s Azure Active Directory k vytvoření jeho spravovanou identitu a nakonfiguruje identitu ve virtuálním počítači.

1.  Vyberte **virtuálního počítače** , které chcete povolit MSI v.  
2.  V levém navigačním panelu klikněte na tlačítko **konfigurace**. 
3.  Zobrazí **identita spravované služby**. Registrovat a povolit soubor MSI, vyberte **Ano**, pokud chcete zakázat, vyberte Ne. 
4.  Ujistěte se, kliknete na tlačítko **Uložit** konfiguraci uložíte.  

    ![Obrázek alternativní text](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Udělit přístup virtuálních počítačů tajného klíče uložené v Key Vault 
 
Pomocí Instalační služby MSI kódu můžete získat přístupové tokeny k ověřování k prostředkům, které podporují ověřování Azure AD.  Ne všechny služby Azure, ale podporují ověřování Azure AD. Chcete-li MSI pomocí těchto služeb, uložit přihlašovací údaje služby Azure Key Vault a použijte MSI pro přístup k Key Vault se načíst přihlašovací údaje. 

Nejprve musíme vytvořit Key Vault a udělit přístup identity naše Virtuálního počítače do služby Key Vault.   

1. V horní části na levém navigačním panelu, vyberte **vytvořit prostředek** > **zabezpečení a identita** > **Key Vault**.  
2. Zadejte **název** pro nové Key Vault. 
3. Vyhledejte Key Vault ve stejné skupině předplatného a prostředků jako virtuální počítač, který jste vytvořili dříve. 
4. Vyberte **zásady přístupu** a klikněte na tlačítko **přidat nový**. 
5. V konfigurace ze šablony, vyberte **správu tajný klíč**. 
6. Zvolte **vyberte hlavní**a do pole vyhledávání zadejte název virtuálního počítače, které jste vytvořili dříve.  V seznamu výsledků vyberte virtuální počítač a klikněte na tlačítko **vyberte**. 
7. Klikněte na tlačítko **OK** k dokončení přidání nové zásady přístupu a **OK** dokončete výběr zásad přístupu. 
8. Klikněte na tlačítko **vytvořit** dokončete vytváření Key Vault. 

    ![Obrázek alternativní text](../media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


V dalším kroku přidejte tajného klíče do služby Key Vault, tak, aby později můžete načíst tajný klíč pomocí kód spuštěný ve vašem virtuálním počítači: 

1. Vyberte **všechny prostředky**a najděte a vyberte Key Vault, které jste vytvořili. 
2. Vyberte **tajné klíče**a klikněte na tlačítko **přidat**. 
3. Vyberte **ruční**, z **možnosti odesílání**. 
4. Zadejte název a hodnota pro tajný klíč.  Hodnota může být nic, co chcete. 
5. Nechte aktivace datum a datum vypršení platnosti, zrušte zaškrtnutí a nechte **povoleno** jako **Ano**. 
6. Klikněte na tlačítko **vytvořit** vytvořit tajný klíč. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Získání přístupového tokenu pomocí identity virtuálního počítače a použít ho k načtení tajného klíče z trezoru klíčů  

Pokud nemáte prostředí PowerShell 4.3.1 nebo vyšší, budete muset [stáhněte a nainstalujte nejnovější verzi](https://docs.microsoft.com/powershell/azure/overview).

První jsme MSI Virtuálního počítače použít k získání přístupového tokenu k ověření Key Vault:
 
1. Na portálu, přejděte na **virtuální počítače** a přejděte k virtuálnímu počítači Windows a v **přehled**, klikněte na tlačítko **Connect**.
2. Zadejte ve vaší **uživatelské jméno** a **heslo** pro které jste přidali při vytváření **virtuální počítač s Windows**.  
3. Teď, když jste vytvořili **připojení ke vzdálené ploše** s virtuálním počítačem, otevřete v této relaci vzdálené prostředí PowerShell.  
4. V prostředí PowerShell vyvolání webové žádosti na klienta se získat token pro místního hostitele v specifického portu pro virtuální počítač.  

    Žádost o prostředí PowerShell:
    
    ```powershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
    ```
    
    V dalším kroku extrahujte úplnou odpověď, který je uložený jako řetězec formátu JavaScript objekt Notation (JSON) v objektu $response.  
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json 
    ```
    
    V dalším kroku extrahujte tokenu přístupu z odpovědi.  
    
    ```powershell
    $KeyVaultToken = $content.access_token 
    ```
    
    Nakonec pomocí Powershellu příkaz Invoke-WebRequest načíst tajný klíč, který jste vytvořili dříve v Key Vault, předávání přístupový token v hlavičce autorizace.  Budete potřebovat adresu URL služby Key Vault, která se **Essentials** části **přehled** stránka služby Key Vault.  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    Odpověď bude vypadat takto: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Jakmile jste načíst tajného klíče z trezoru klíčů, můžete ke svému ověření u služby, která vyžaduje zadání jména a hesla. 

## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](overview.md).

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.
