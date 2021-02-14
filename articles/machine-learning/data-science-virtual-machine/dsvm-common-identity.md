---
title: Nastavení společné identity
titleSuffix: Azure Data Science Virtual Machine
description: Naučte se vytvářet běžné uživatelské účty, které se dají používat v rámci více Virtual Machines pro datové vědy. K ověřování uživatelů v Data Science Virtual Machine můžete použít Azure Active Directory nebo místní službu Active Directory.
keywords: obsáhlý Learning, AI, nástroje pro datové vědy, virtuální počítač pro datové vědy, geoprostorové analýzy, vědecké zpracování týmových dat
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 43ef0f55f0b0f3ff9008f0ddf240ad0ea4f37df6
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519724"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Nastavení společné identity na Data Science Virtual Machine

Na Microsoft Azure virtuálním počítači (VM), včetně Data Science Virtual Machine (DSVM), vytvoříte během zřizování virtuálního počítače místní uživatelské účty. Pomocí těchto přihlašovacích údajů se uživatelé pak ověřují na virtuálním počítači. Pokud máte více virtuálních počítačů, ke kterým potřebují uživatelé přístup, může Správa přihlašovacích údajů velmi nenáročných. Vynikajícím řešením je nasazení běžných uživatelských účtů a správy prostřednictvím zprostředkovatele identity založeného na standardech. Prostřednictvím tohoto přístupu můžete použít jedinou sadu přihlašovacích údajů pro přístup k více prostředkům v Azure, včetně několika DSVMs.

Služba Active Directory je oblíbený poskytovatel identity a podporuje se v Azure jako cloudová služba i jako místní adresář. K ověřování uživatelů na samostatném DSVM nebo clustere DSVMs v sadě škálování virtuálního počítače Azure můžete použít Azure Active Directory (Azure AD) nebo místní službu Active Directory. Provedete to připojením instancí DSVM k doméně služby Active Directory.

Pokud již máte službu Active Directory, můžete ji použít jako svého společného poskytovatele identity. Pokud nemáte službu Active Directory, můžete v Azure spustit spravovanou instanci služby Active Directory prostřednictvím [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) (Azure služba AD DS).

Dokumentace k [Azure AD](../../active-directory/index.yml) poskytuje podrobné [pokyny pro správu](../../active-directory/hybrid/whatis-hybrid-identity.md), včetně pokynů k propojení Azure AD s vaším místním adresářem, pokud ho máte.

Tento článek popisuje, jak nastavit plně spravovanou službu Active Directory Domain Service v Azure pomocí služba AD DS Azure. Pak můžete své DSVMs připojit k spravované doméně služby Active Directory. Tento přístup umožňuje uživatelům přístup k fondu DSVMs (a dalších prostředků Azure) prostřednictvím běžného uživatelského účtu a přihlašovacích údajů.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Nastavení plně spravované domény služby Active Directory v Azure

Azure služba AD DS usnadňuje správu identit tím, že poskytuje plně spravovanou službu v Azure. V této doméně služby Active Directory budete spravovat uživatele a skupiny. Pokud chcete ve svém adresáři nastavit doménu služby Active Directory hostované v Azure a uživatelské účty, postupujte takto:

1. V Azure Portal přidejte uživatele do služby Active Directory: 

   1. Přihlaste se k [centru pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí účtu, který je globálním správcem adresáře.
    
   1. Vyberte **Azure Active Directory** a potom **Uživatelé a skupiny**.
    
   1. V **Uživatelé a skupiny** vyberte **Všichni uživatelé** a pak vyberte **Nový uživatel**.
   
        Otevře se podokno **uživatel** :
      
        ![Podokno "uživatel"](./media/add-user.png)
    
   1. Zadejte podrobnosti o uživateli, například **Jméno** a **Uživatelské jméno**. Část názvu domény uživatelského jména musí být buď počáteční výchozí název domény "[název domény].. Microsoft. com" nebo ověřený nefederovaný [vlastní název domény](../../active-directory/fundamentals/add-custom-domain.md) , například "contoso.com".
    
   1. Zkopírujte nebo si jiným způsobem poznamenejte vygenerované heslo uživatele, abyste ho mohli po dokončení tohoto procesu poskytnout příslušnému uživateli.
    
   1. Můžete ale také otevřít a vyplnit informace o uživateli v části **Profil**, **Skupiny** nebo **Role adresáře**. 
    
   1. V části **uživatel** vyberte **vytvořit**.
    
   1. Vygenerované heslo bezpečně distribuujte novému uživateli, aby se mohli přihlásit.

1. Vytvořte instanci služby Azure služba AD DS. Postupujte podle pokynů v části  [povolení Azure Active Directory Domain Services pomocí Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md) (oddíl vytvořit instanci a nakonfigurovat základní nastavení). Je důležité aktualizovat stávající hesla uživatelů ve službě Active Directory tak, aby se heslo ve službě Azure služba AD DS synchronizoval. Je také důležité přidat DNS do Azure služba AD DS, jak je popsáno v části "vyplnění polí v okně základy Azure Portal k vytvoření instance služby Azure služba AD DS v této části.

1. Vytvořte samostatnou podsíť DSVM ve virtuální síti vytvořené v části Vytvoření a konfigurace virtuální sítě v předchozím kroku.
1. V podsíti DSVM vytvořte jednu nebo víc instancí DSVM.
1. Podle [pokynů](../../active-directory-domain-services/join-ubuntu-linux-vm.md) přidejte DSVM do Active Directory. 
1. Připojte sdílenou složku služby soubory Azure pro hostování domovského adresáře nebo adresáře poznámkového bloku, aby se váš pracovní prostor mohl připojit k libovolnému počítači. (Pokud potřebujete vysoké oprávnění na úrovni souborů, budete potřebovat systém souborů NFS (Network File System), který je spuštěný na jednom nebo víc virtuálních počítačích.)

   1. [Vytvořte sdílenou složku služby soubory Azure](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Připojte tuto sdílenou složku na DSVM Linux. Když vyberete **připojit** pro sdílenou složku Azure Files v účtu úložiště v Azure Portal, zobrazí se příkaz, který se spustí v prostředí bash v systému Linux DSVM. Příkaz vypadá takto:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Předpokládejme například, že jste připojili sdílenou složku souborů Azure v/data/Workspace.. Teď vytvořte adresáře pro každého uživatele ve sdílené složce:/data/Workspace/user1,/data/Workspace/User2 a tak dále. Vytvořte `notebooks` adresář v pracovním prostoru každého uživatele. 
1. Vytvořte symbolické odkazy pro `notebooks` v `$HOME/userx/notebooks/remote` .   

Nyní máte uživatele v instanci služby Active Directory hostované v Azure. Pomocí přihlašovacích údajů služby Active Directory se uživatelé můžou přihlásit k jakýmkoli DSVM (SSH nebo JupyterHub), který je připojený k Azure služba AD DS. Vzhledem k tomu, že se pracovní prostor uživatele nachází ve sdílené složce služby soubory Azure, uživatelé mají přístup ke svým notebookům a jiné práci z libovolného DSVMu, když používají JupyterHub.

Pro automatické škálování můžete pomocí sady škálování virtuálních počítačů vytvořit fond virtuálních počítačů, které jsou všechny připojené k doméně tímto způsobem a se sdíleným diskem připojeným. Uživatelé se mohou přihlásit k libovolnému dostupnému počítači v sadě škálování virtuálního počítače a mít přístup ke sdílenému disku, na kterém jsou uloženy poznámkové bloky. 

## <a name="next-steps"></a>Další kroky

* [Bezpečné uložení přihlašovacích údajů pro přístup k prostředkům cloudu](dsvm-secure-access-keys.md)