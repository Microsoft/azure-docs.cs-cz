---
title: Nastavte si společnou identitu pro virtuální počítač pro datové vědy – Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit běžné uživatelské účty, které je možné napříč více virtuální počítače pro datové vědy. Azure Active Directory nebo místní služby Active Directory můžete použít k ověřování uživatelů pro virtuální počítač pro datové vědy.
keywords: obsáhlý learning, AI, nástrojů pro datové vědy, virtuální počítač pro datové vědy, geoprostorové analýzy, vědecké zpracování týmových dat
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 6be7c63d3879c7ed89cd97eaecd6d59b6b5aadd4
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53075467"
---
# <a name="set-up-a-common-identity-on-the-data-science-virtual-machine"></a>Nastavte si společnou identitu na virtuální počítač pro datové vědy

Na virtuálním počítači Azure (VM), včetně Data Science virtuálního počítače (datové VĚDY) vytvořte místní uživatelské účty při zřizování virtuálního počítače. Uživatelé ověřovat pak k virtuálnímu počítači s použitím těchto přihlašovacích údajů. Pokud máte více virtuálních počítačů, které potřebujete pro přístup k tomu můžete rychle získat náročné při správě přihlašovacích údajů. Běžné uživatelské účty a správy prostřednictvím zprostředkovatele identit umožňují používat jednu sadu přihlašovacích údajů pro přístup k více prostředkům v Azure, včetně více datové. 

Active Directory je poskytovatel oblíbených identity a je podporovaný v Azure jako službu a místní. Můžete použít Azure Active Directory (Azure AD) nebo místní služby Active Directory k ověřování uživatelů na samostatný DSVM nebo cluster datové sady škálování virtuálních počítačů Azure. To provedete přihlášením k instance DSVM k doméně služby Active Directory. 

Pokud už máte služby Active Directory ke správě identit, můžete jako běžné zprostředkovatele identity. Pokud nemáte k dispozici služby Active Directory, můžete si projít spravovanou instanci služby Active Directory v Azure služby zvané [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS). 

V dokumentaci k [Azure AD](https://docs.microsoft.com/azure/active-directory/) poskytuje podrobné [správu pokyny](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity), včetně připojení k místní adresář Azure AD, pokud nějakou máte. 

Tento článek popisuje postup nastavení plně spravované domény služby Active Directory v Azure pomocí služby Azure AD DS. Pak můžete připojit vaše datové ke spravované doméně služby Active Directory umožňuje uživatelům přístup k fondu datové (a dalších prostředků Azure) pomocí společného uživatelského účtu a přihlašovací údaje. 

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Nastavení plně spravované domény služby Active Directory v Azure

Azure AD DS zjednodušují správu identit tím, že poskytuje plně spravovanou službu v Azure. V této doméně služby Active Directory Správa uživatelů a skupin. Postup nastavení hostované v Azure Active Directory doména a uživatelské účty ve vašem adresáři jsou:

1. Na webu Azure Portal přidejte uživatele do služby Active Directory: 

   a. Přihlaste se k [centru pro správu služby Azure Active Directory](https://aad.portal.azure.com) prostřednictvím účtu, který má k adresáři oprávnění globálního správce.
    
   b. Vyberte **Azure Active Directory** a potom **Uživatelé a skupiny**.
    
   c. V okně **Uživatelé a skupiny** vyberte **Všichni uživatelé** a potom vyberte **Nový uživatel**.
   
      **Uživatele** se otevře podokno.
      
      ![V podokně "User"](./media/add-user.png)
    
   d. Zadejte podrobnosti o uživateli, například **Jméno** a **Uživatelské jméno**. Část názvu domény, uživatelského jména musí být počáteční výchozí doména název "[název domény].onmicrosoft.com" nebo ověřený, jiné než federované [vlastního názvu domény](../../active-directory/add-custom-domain.md) jako je například "contoso.com".
    
   e. Zkopírujte nebo si jiným způsobem poznamenejte vygenerované heslo uživatele, abyste ho mohli po dokončení tohoto procesu poskytnout příslušnému uživateli.
    
   f. Můžete ale také otevřít a vyplnit informace o uživateli v části **Profil**, **Skupiny** nebo **Role adresáře**. 
    
   g. V části **Uživatel** vyberte **Vytvořit**.
    
   h. Bezpečně předejte vygenerované heslo novému uživateli, aby se mohl přihlásit.

1. Vytvoření instance služby Azure AD DS. Postupujte podle pokynů v článku [povolit Azure Active Directory Domain Services pomocí webu Azure portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (úloh 1 až 5). Je důležité aktualizovat existující uživatelská hesla ve službě Active Directory tak, aby se synchronizuje hesla ve službě Azure AD DS. Je také potřeba přidat do služby Azure AD DS, DNS jak je popsáno v úloze 4 z článku. 

1. Vytvořte samostatnou podsíť DSVM ve virtuální síti vytvořené v úloze 2 v předchozím kroku.
1. Vytvořte jeden nebo více instancí virtuálního počítače pro datové vědy v podsíti DSVM. 
1. Postupujte podle [pokyny](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) přidání DSVM do služby Active Directory. 
1. Připojení sdílené složky služby soubory Azure k hostování adresáři doma nebo v poznámkovém bloku umožňující připojení pracovního prostoru na jakýkoli počítač. (Pokud potřebujete úzkou oprávnění na úrovni souboru, budete potřebovat jeden nebo více virtuálních počítačů se systémem systému souborů NFS.)

   a. [Vytvoření sdílené složky Azure Files](../../storage/files/storage-how-to-create-file-share.md).
    
   b. Připojte na datové VĚDY pro Linux. Když vyberete **připojit** tlačítko pro soubory Azure sdílené složky v účtu úložiště na webu Azure Portal, příkaz ke spuštění v Bashe se zobrazí prostředí na datové VĚDY pro Linux. Příkaz vypadá takto:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Předpokládejme, připojí vaše sdílené složky Azure Files v /data/workspace, třeba. Teď vytvořte adresáře pro každý z vašich uživatelů ve sdílené složce: /data/workspace/user1 /data/workspace/user2 a tak dále. Vytvoření `notebooks` adresář v prostoru jednotlivých uživatelů. 
1. Vytvořit symbolické odkazy pro `notebooks` v `$HOME/userx/notebooks/remote`.   

Teď máte uživatelé ve vaší instanci služby Active Directory hostované v Azure. S použitím přihlašovacích údajů služby Active Directory, uživatelé mohou přihlásit k jakékoli DSVM (SSH nebo JupyterHub), který je připojen ke službě Azure AD DS. Vzhledem k tomu, že pracovní prostor uživatele je ve sdílené složce služby soubory Azure, uživatelé mají přístup k jejich poznámkových bloků a další práci z jakékoli DSVM při jejich použití JupyterHub. 

Pro automatické škálování můžete použít škálovací sadu virtuálních počítačů k vytvoření fondu virtuálních počítačů, které jsou připojené k doméně tímto způsobem a se sdíleným diskem připojený. Uživatelé mohli přihlásit do libovolného dostupné počítače ve škálovací sadě virtuálních počítačů a mají přístup k sdíleného disku, kde jsou uloženy poznámkové. 

## <a name="next-steps"></a>Další postup

* [Bezpečně ukládat přihlašovací údaje pro přístup ke cloudovým prostředkům](dsvm-secure-access-keys.md)



