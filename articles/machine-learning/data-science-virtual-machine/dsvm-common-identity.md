---
title: Instalační program společnou identitu pro datové vědy virtuálního počítače – Azure | Microsoft Docs
description: Instalační program v prostředích enterprise team DSVM společnou identitu.
keywords: přímý učení AI datové vědy nástroje, data vědecké účely virtuální počítač, geoprostorové analýzy, proces team dat vědecké účely
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 431d3079c0d942c15ccbc8352261ccfe1f5f6e47
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="setup-common-identity-on-the-data-science-vm"></a>Instalační program společnou identitu ve virtuálním počítači Data vědecké účely

Ve výchozím nastavení na virtuálním počítači Azure včetně místního uživatele datové vědy virtuálních počítačů (DSVM) účty jsou vytvořeny při zřizování virtuálního počítače a uživatele ověřovat na virtuální počítač pomocí těchto přihlašovacích údajů. Pokud máte více virtuálních počítačů, které potřebujete získat přístup, tento postup můžete rychle získat komplikované ke správě přihlašovacích údajů. Běžné uživatelské účty a správu pomocí zprostředkovatele identity založených na standardech umožňuje používat jedinou sadu pověření pro přístup k více prostředkům v Azure, včetně více DSVMs. 

Active Directory (AD) je zprostředkovatele oblíbených identity a je podporovaná i v Azure jako služba, jakož i místní. Můžete využít k ověření uživatelů na samostatný počítač vědecké účely dat (DSVM) nebo cluster DSVM na sadu škálování virtuálního počítače Azure AD ani Azure AD. K tomu je potřeba DSVM instance připojení k doméně AD. Pokud již máte služby Active Directory ke správě identit, můžete ho použít jako běžné zprostředkovatele identity. V případě, že jste AD, můžete spustit spravované AD v Azure pomocí služby názvem [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/)(Azure AD DS). 

V dokumentaci k [Azure Active directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/) poskytuje podrobné [pokyny](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity) spravovat služby active directory včetně připojení k místní adresář Azure AD, pokud nemáte. 

Zbývající část tohoto článku popisuje kroky, jak nastavit prostřednictvím plně spravované domény služby AD v Azure pomocí služby Azure AD DS a připojení k vaší DSVMs k spravované doméně AD povolit uživatelům přístup k fondu DSVMs (a dalším prostředkům služby Azure) pomocí společného uživatelského účtu a přihlašovací údaje. 

##  <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Nastavení plně spravované domény služby Active Directory v Azure

Azure AD DS můžete snadno spravovat identity vašich tím, že poskytuje plně spravované služby v Azure. V této doméně služby Active directory se spravují uživatelé a skupiny.  Postup nastavení Azure hostované AD domény a uživatelské účty ve vašem adresáři:

1. Přidání uživatele do služby Active directory na portálu. 

    a. Přihlaste se k [centra pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí účtu, který je globální správce adresáře.
    
    b. Vyberte **Azure Active Directory** a potom **uživatelů a skupin**.
    
    c. Na **uživatelů a skupin**, vyberte **všichni uživatelé**a potom vyberte **nového uživatele**.
   
   ![Použití příkazu Přidat](./media/add-user.png)
    
    d. Zadejte podrobnosti pro uživatele, jako například **název** a **uživatelské jméno**. Část názvu domény uživatelského jména musí buď musí být počáteční výchozí domény název "[název domény].onmicrosoft.com" nebo ověřené, nefederovaných [vlastní název domény](../../active-directory/add-custom-domain.md) například "contoso.com".
    
    e. Zkopírujte nebo jinak Poznámka: hesla generovaného uživatele tak, aby ji mohli nabídnout uživatele po dokončení tohoto procesu.
    
    f. Volitelně můžete otevřít a vyplňte informace v **profil**, **skupiny**, nebo **Directory role** pro uživatele. 
    
    g. Na **uživatele**, vyberte **vytvořit**.
    
    h. Bezpečně distribuujte vygenerované heslo pro nového uživatele tak, aby uživatel moct přihlásit.

2.  Vytvoření služby Azure AD Domain Services

    Chcete-li vytvořit přidá Azure, postupujte podle pokynů v článku "[povolit Azure Active Directory Domain Services pomocí webu Azure portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)" (úloh 1 až 5 úloh). Je důležité, aby stávající hesla uživatele ve službě Active directory jsou aktualizovány tak, aby je synchronizována hesla ve službě Azure AD DS. Také je potřeba přidat DNS do Azure AD DS, jak je uvedeno v úloh č. 4 výše článku. 

3.  Vytvořit samostatnou podsíť DSVM ve virtuální síti vytvořené v úloze 2 # předcházející kroku
4.  Vytvořte jeden nebo více instancí vědecké účely Data virtuálního počítače v podsíti DSVM 
5.  Postupujte podle [pokyny](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) přidat DSVM do služby AD. 
6.  Připojte další sdílené soubory Azure k hostování adresáře home nebo poznámkového bloku povolit připojení pracovního prostoru na libovolném počítači. (Pokud budete potřebovat oprávnění na úrovni souborů úzkou budete potřebovat systému souborů NFS spuštěna na jeden nebo více virtuálních počítačů)

    a. [Vytvořte sdílenou složku Azure](../../storage/files/storage-how-to-create-file-share.md)
    
    b. Připojte je na Linux DSVM. Když kliknete na tlačítko "Připojit" pro soubory Azure ve vašem účtu úložiště na portálu Azure, se zobrazí příkaz ke spuštění v prostředí bash na Linux DSVM. Příkaz bude vypadat takto:
```
sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
```
7.  Řekněme, připojené soubory Azure v /data/workspace. Teď vytvořte adresáře pro každý z vašich uživatelů ve sdílené složce. /data/Workspace/user1, /data/workspace/user2 a tak dále. Vytvoření ```notebooks``` adresář v prostoru pro každého uživatele. 
8. Vytvoření symbolické odkazy ```notebooks``` v ```$HOME/userx/notebooks/remote```.   

Nyní máte uživatele ve vašem active directory, které jsou hostované v Azure a moct přihlásit k žádné DSVM (SSH, Jupyterhub), který je připojen k Azure AD DS pomocí přihlašovacích údajů, AD. Vzhledem k tomu, že prostoru uživatele je na sdílené soubory Azure, bude mít uživatel přístup k jejich poznámkových bloků a další pracovní z jakékoli DSVM při použití Jupyterhub. 

Pro automatické škálování můžete použít k vytvoření fondu virtuálních počítačů, které jsou připojené k doméně tímto způsobem a pomocí sdíleného disku, které jsou připojené sad škálování virtuálního počítače. Uživatelé přihlášení k žádnému počítači k dispozici v sadě škálování virtuálního počítače a mít přístup k sdíleného disku, kam se ukládají jejich poznámkových bloků. 

## <a name="next-steps"></a>Další postup

* [Bezpečně uložit přihlašovací údaje pro přístup k prostředkům cloudu](dsvm-secure-access-keys.md)



