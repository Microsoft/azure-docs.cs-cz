---
title: Nastavení společné identity
titleSuffix: Azure Data Science Virtual Machine
description: Zjistěte, jak vytvořit běžné uživatelské účty, které je možné napříč více virtuální počítače pro datové vědy. Azure Active Directory nebo místní služby Active Directory můžete použít k ověřování uživatelů pro virtuální počítač pro datové vědy.
keywords: obsáhlý learning, AI, nástrojů pro datové vědy, virtuální počítač pro datové vědy, geoprostorové analýzy, vědecké zpracování týmových dat
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 44f1f7ae3b290e1dbf01877f3881e1d95a238446
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208145"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Nastavení společné identity na Data Science Virtual Machine

Na Microsoft Azure virtuálním počítači (VM), včetně Data Science Virtual Machine (DSVM), vytvoříte během zřizování virtuálního počítače místní uživatelské účty. Uživatelé ověřovat pak k virtuálnímu počítači s použitím těchto přihlašovacích údajů. Pokud máte více virtuálních počítačů, ke kterým potřebují uživatelé přístup, může Správa přihlašovacích údajů velmi nenáročných. Vynikajícím řešením je nasazení běžných uživatelských účtů a správy prostřednictvím zprostředkovatele identity založeného na standardech. Prostřednictvím tohoto přístupu můžete použít jedinou sadu přihlašovacích údajů pro přístup k více prostředkům v Azure, včetně několika DSVMs.

Služba Active Directory je oblíbený poskytovatel identity a podporuje se v Azure jako cloudová služba i jako místní adresář. Můžete použít Azure Active Directory (Azure AD) nebo místní služby Active Directory k ověřování uživatelů na samostatný DSVM nebo cluster datové sady škálování virtuálních počítačů Azure. To provedete přihlášením k instance DSVM k doméně služby Active Directory.

Pokud již máte službu Active Directory, můžete ji použít jako svého společného poskytovatele identity. Pokud nemáte službu Active Directory, můžete v Azure spustit spravovanou instanci služby Active Directory prostřednictvím [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure služba AD DS).

Dokumentace k [Azure AD](https://docs.microsoft.com/azure/active-directory/) poskytuje podrobné [pokyny pro správu](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution), včetně pokynů k propojení Azure AD s vaším místním adresářem, pokud ho máte.

Tento článek popisuje, jak nastavit plně spravovanou službu Active Directory Domain Service v Azure pomocí služba AD DS Azure. Pak můžete své DSVMs připojit k spravované doméně služby Active Directory. Tento přístup umožňuje uživatelům přístup k fondu DSVMs (a dalších prostředků Azure) prostřednictvím běžného uživatelského účtu a přihlašovacích údajů.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Nastavení plně spravované domény služby Active Directory v Azure

Azure AD DS zjednodušují správu identit tím, že poskytuje plně spravovanou službu v Azure. V této doméně služby Active Directory Správa uživatelů a skupin. Pokud chcete ve svém adresáři nastavit doménu služby Active Directory hostované v Azure a uživatelské účty, postupujte takto:

1. Na webu Azure Portal přidejte uživatele do služby Active Directory: 

   1. Přihlaste se k [centru pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí účtu, který je globálním správcem adresáře.
    
   1. Vyberte **Azure Active Directory** a potom **Uživatelé a skupiny**.
    
   1. V **Uživatelé a skupiny**vyberte **Všichni uživatelé**a pak vyberte **Nový uživatel**.
   
           The **User** pane opens:
      
      ![V podokně "User"](./media/add-user.png)
    
   1. Zadejte podrobnosti o uživateli, například **Jméno** a **Uživatelské jméno**. Část názvu domény, uživatelského jména musí být počáteční výchozí doména název "[název domény].onmicrosoft.com" nebo ověřený, jiné než federované [vlastního názvu domény](../../active-directory/add-custom-domain.md) jako je například "contoso.com".
    
   1. Zkopírujte nebo si jiným způsobem poznamenejte vygenerované heslo uživatele, abyste ho mohli po dokončení tohoto procesu poskytnout příslušnému uživateli.
    
   1. Můžete ale také otevřít a vyplnit informace o uživateli v části **Profil**, **Skupiny** nebo **Role adresáře**. 
    
   1. V části **uživatel**vyberte **vytvořit**.
    
   1. Vygenerované heslo bezpečně distribuujte novému uživateli, aby se mohli přihlásit.

1. Vytvoření instance služby Azure AD DS. Postupujte podle pokynů v části [povolení Azure Active Directory Domain Services pomocí Azure Portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (oddíl vytvořit instanci a nakonfigurovat základní nastavení). Je důležité aktualizovat existující uživatelská hesla ve službě Active Directory tak, aby se synchronizuje hesla ve službě Azure AD DS. Je také důležité přidat DNS do Azure služba AD DS, jak je popsáno v části "vyplnění polí v okně základy Azure Portal k vytvoření instance služby Azure služba AD DS v této části.

1. Vytvořte samostatnou podsíť DSVM ve virtuální síti vytvořené v části Vytvoření a konfigurace virtuální sítě v předchozím kroku.
1. V podsíti DSVM vytvořte jednu nebo víc instancí DSVM.
1. Podle [pokynů](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) přidejte DSVM do Active Directory. 
1. Připojte sdílenou složku služby soubory Azure pro hostování domovského adresáře nebo adresáře poznámkového bloku, aby se váš pracovní prostor mohl připojit k libovolnému počítači. (Pokud potřebujete vysoké oprávnění na úrovni souborů, budete potřebovat systém souborů NFS (Network File System), který je spuštěný na jednom nebo víc virtuálních počítačích.)

   1. [Vytvoření sdílené složky Azure Files](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Připojte tuto sdílenou složku na DSVM Linux. Když vyberete **připojit** pro sdílenou složku Azure Files v účtu úložiště v Azure Portal, zobrazí se příkaz, který se spustí v prostředí bash v systému Linux DSVM. Příkaz vypadá takto:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Předpokládejme například, že jste připojili sdílenou složku souborů Azure v/data/Workspace. Teď vytvořte adresáře pro každého uživatele ve sdílené složce:/data/Workspace/user1,/data/Workspace/User2 a tak dále. Vytvoření `notebooks` adresář v prostoru jednotlivých uživatelů. 
1. Vytvořit symbolické odkazy pro `notebooks` v `$HOME/userx/notebooks/remote`.   

Nyní máte uživatele v instanci služby Active Directory hostované v Azure. Pomocí přihlašovacích údajů služby Active Directory se uživatelé můžou přihlásit k jakýmkoli DSVM (SSH nebo JupyterHub), který je připojený k Azure služba AD DS. Vzhledem k tomu, že pracovní prostor uživatele je ve sdílené složce služby soubory Azure, uživatelé mají přístup k jejich poznámkových bloků a další práci z jakékoli DSVM při jejich použití JupyterHub.

Pro automatické škálování můžete použít škálovací sadu virtuálních počítačů k vytvoření fondu virtuálních počítačů, které jsou připojené k doméně tímto způsobem a se sdíleným diskem připojený. Uživatelé se mohou přihlásit k libovolnému dostupnému počítači v sadě škálování virtuálního počítače a mít přístup ke sdílenému disku, na kterém jsou uloženy poznámkové bloky. 

## <a name="next-steps"></a>Další kroky

* [Bezpečně ukládat přihlašovací údaje pro přístup ke cloudovým prostředkům](dsvm-secure-access-keys.md)



