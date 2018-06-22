---
title: Nastavit společnou identitu pro datové vědy virtuálního počítače – Azure | Microsoft Docs
description: Nastavte společnou identitu v prostředích DSVM enterprise týmu.
keywords: přímý učení AI datové vědy nástroje, data vědecké účely virtuální počítač, geoprostorové analýzy, proces team dat vědecké účely
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: d6235f3a425481a13e627d683bb4c3943b473b40
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309824"
---
# <a name="set-up-a-common-identity-on-the-data-science-virtual-machine"></a>Nastavit společnou identitu na Data virtuálního počítače vědecké účely

Na virtuální počítač Azure (VM), včetně na datové vědě virtuálního počítače (DSVM) vytvořte místní uživatelské účty při zřizování virtuálního počítače. Uživatelé pak ověřit, aby virtuální počítač pomocí těchto přihlašovacích údajů. Pokud máte více virtuálních počítačů, které potřebujete získat přístup, tento postup můžete rychle získat komplikované při správě přihlašovacích údajů. Běžné uživatelské účty a správu prostřednictvím poskytovatele identity založených na standardech umožňují používat jedinou sadu pověření pro přístup k více prostředkům v Azure, včetně více DSVMs. 

Služby Active Directory je zprostředkovatele oblíbených identity a je podporovaná v Azure jako službu a místní. Můžete použít Azure Active Directory (Azure AD) nebo místní služby Active Directory k ověřování uživatelů na samostatnou DSVM nebo cluster DSVMs sady škálování virtuálního počítače Azure. To provedete DSVM instance připojení k doméně služby Active Directory. 

Pokud již máte služby Active Directory ke správě identit, můžete ho použít jako běžné zprostředkovatele identity. Pokud nemáte služby Active Directory, můžete spustit spravované instance služby Active Directory v Azure pomocí služby názvem [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS). 

V dokumentaci k [Azure AD](https://docs.microsoft.com/azure/active-directory/) poskytuje podrobné [správu pokyny](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity), včetně připojení k místní adresář Azure AD, pokud nemáte. 

Tento článek popisuje kroky pro nastavení plně spravovaná služba domény služby Active Directory v Azure pomocí služby Azure AD DS. Vaše DSVMs může potom připojí k spravované doméně služby Active Directory umožňuje uživatelům přístup k fondu DSVMs (a dalším prostředkům služby Azure) pomocí společného uživatelského účtu a přihlašovací údaje. 

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Nastavení plně spravované domény služby Active Directory v Azure

Azure AD DS můžete snadno spravovat identity vašich tím, že poskytuje plně spravované služby v Azure. V této doméně služby Active Directory můžete spravovat uživatele a skupiny. Postup nastavení hostované v Azure Active Directory doménu a uživatelské účty ve vašem adresáři jsou:

1. Na portálu Azure přidejte uživatele do služby Active Directory: 

   a. Přihlaste se k [centra pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí účtu, který je globální správce adresáře.
    
   b. Vyberte **Azure Active Directory** a potom **uživatelů a skupin**.
    
   c. Na **uživatelů a skupin**, vyberte **všichni uživatelé**a potom vyberte **nového uživatele**.
   
      **Uživatele** otevře se podokno.
      
      ![V podokně "User"](./media/add-user.png)
    
   d. Zadejte podrobnosti pro uživatele, jako například **název** a **uživatelské jméno**. Část názvu domény uživatelské jméno musí být buď výchozí počáteční domény název "[název domény].onmicrosoft.com" nebo ověřené, nefederovaných [vlastní název domény](../../active-directory/add-custom-domain.md) například "contoso.com".
    
   e. Zkopírujte nebo jinak Poznámka: hesla generovaného uživatele tak, aby ji mohli nabídnout uživatele po dokončení tohoto procesu.
    
   f. Volitelně můžete otevřít a vyplňte informace v **profil**, **skupiny**, nebo **Directory role** pro uživatele. 
    
   g. Na **uživatele**, vyberte **vytvořit**.
    
   h. Bezpečně distribuujte vygenerované heslo pro nového uživatele tak, aby uživatel moct přihlásit.

2. Vytvoření instance služby Azure AD DS. Postupujte podle pokynů v článku [povolit Azure Active Directory Domain Services pomocí webu Azure portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (úloh 1 až 5). Je potřeba aktualizovat stávající hesla uživatele ve službě Active Directory tak, aby se synchronizuje hesla ve službě Azure AD DS. Je také důležité přidat DNS ve službě Azure AD DS, jak je popsáno v článku úloha 4. 

3. Vytvořte samostatnou podsíť DSVM ve virtuální síti vytvořené v úloze 2 v předchozím kroku.
4. Vytvořte jeden nebo více instancí vědecké účely Data virtuálního počítače v podsíti DSVM. 
5. Postupujte podle [pokyny](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) přidání DSVM do služby Active Directory. 
6. Připojení Azure Files složky pro hostování adresáře home nebo poznámkového bloku povolit připojení pracovního prostoru na libovolném počítači. (Pokud budete potřebovat úzkou oprávnění na úrovni souborů, budete potřebovat systém souborů NFS spuštěné na jeden nebo více virtuálních počítačů.)

   a. [Vytvoření složky Azure Files](../../storage/files/storage-how-to-create-file-share.md).
    
   b. Připojte je na Linux DSVM. Když vyberete **Connect** tlačítko pro sdílení souborů Azure ve vašem účtu úložiště na portálu Azure, příkaz ke spuštění v Bash prostředí Linux DSVM se zobrazí. Příkaz vypadá takto:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
7. Předpokládejme třeba připojit vaše soubory Azure sdílenou složku v /data/workspace. Teď vytvořte adresáře pro každý z vašich uživatelů ve sdílené složce: /data/workspace/user1, /data/workspace/user2 a tak dále. Vytvoření `notebooks` adresář v prostoru pro každého uživatele. 
8. Vytvoření symbolické odkazy pro `notebooks` v `$HOME/userx/notebooks/remote`.   

Nyní máte uživatele ve vaší instanci Active Directory, které jsou hostované v Azure. Pomocí pověření služby Active Directory, uživatelé mohou přihlásit k žádné DSVM (SSH nebo JupyterHub), který je připojený ke službě Azure AD DS. Protože prostoru uživatele je ve sdílené složce souborů Azure, uživatelé mají přístup k jejich poznámkových bloků a další pracovní z jakékoli DSVM, když používají JupyterHub. 

Pro automatické škálování můžete použít k vytvoření fondu virtuálních počítačů, které jsou připojené k doméně tímto způsobem a pomocí sdíleného disku připojené škálování virtuálního počítače. Uživatelé přihlášení k žádnému počítači k dispozici v sadě škálování virtuálního počítače a mít přístup k sdíleného disku, kam se ukládají jejich poznámkových bloků. 

## <a name="next-steps"></a>Další postup

* [Bezpečně uložit přihlašovací údaje pro přístup k prostředkům cloudu](dsvm-secure-access-keys.md)



