---
title: Nastavení společné identity
titleSuffix: Azure Data Science Virtual Machine
description: Zjistěte, jak vytvořit běžné uživatelské účty, které lze použít na více virtuálních počítačích pro datové vědy. Pomocí služby Azure Active Directory nebo místní služby Active Directory můžete ověřovat uživatele ve virtuálním počítači pro datové vědy.
keywords: hluboké učení, AI, nástroje pro datovou vědu, virtuální stroj pro datové vědy, geoprostorová analytika, proces vědecké analýzy týmových dat
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 44f1f7ae3b290e1dbf01877f3881e1d95a238446
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70208145"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Nastavení společné identity ve virtuálním počítači datové vědy

Na virtuálním počítači Microsoft Azure (VM), včetně virtuálního počítače pro datové vědy (DSVM), vytvoříte místní uživatelské účty při zřizování virtuálního počítače. Uživatelé se pak ověřují virtuálnímu počítači pomocí těchto pověření. Pokud máte více virtuálních počítačů, ke kterým uživatelé potřebují přístup, správa přihlašovacích údajů může být velmi těžkopádná. Vynikajícím řešením je nasazení běžných uživatelských účtů a správy prostřednictvím poskytovatele identity založeného na standardech. Prostřednictvím tohoto přístupu můžete použít jednu sadu přihlašovacích údajů pro přístup k více prostředků v Azure, včetně více DSVM.

Služba Active Directory je oblíbeným poskytovatelem identity a je podporovaná v Azure jako cloudová služba i jako místní adresář. Azure Active Directory (Azure AD) nebo místní služba Active Directory můžete použít k ověření uživatelů v samostatném zařízení DSVM nebo v clusteru dsvm ů ve škálovací sadě virtuálních počítačů Azure. To provést připojením instancí DSVM k doméně služby Active Directory.

Pokud již službu Active Directory máte, můžete ji použít jako běžného zprostředkovatele identity. Pokud službu Active Directory nemáte, můžete spustit spravovanou instanci služby Active Directory v Azure prostřednictvím [služby Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS).

Dokumentace pro [Azure AD](https://docs.microsoft.com/azure/active-directory/) obsahuje podrobné [pokyny pro správu](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution), včetně pokynů o připojení Azure AD k místnímu adresáři, pokud ho máte.

Tento článek popisuje, jak nastavit plně spravovanou službu domény Active Directory v Azure pomocí Služby Azure AD DS. Potom se můžete připojit ke spravované doméně služby Active Directory. Tento přístup umožňuje uživatelům přístup k fondu DSVMs (a další prostředky Azure) prostřednictvím běžného uživatelského účtu a pověření.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Nastavení plně spravované domény služby Active Directory v Azure

Azure AD DS usnadňuje správu vašich identit tím, že poskytuje plně spravované služby v Azure. V této doméně služby Active Directory spravujete uživatele a skupiny. Chcete-li ve svém adresáři nastavit doménu a uživatelské účty služby Active Directory hostované v Azure, postupujte takto:

1. Na webu Azure Portal přidejte uživatele do služby Active Directory: 

   1. Přihlaste se k [Centru pro správu Služby Azure Active Directory](https://aad.portal.azure.com) pomocí účtu, který je globálním správcem adresáře.
    
   1. Vyberte **Azure Active Directory** a potom **Uživatelé a skupiny**.
    
   1. V **možnostech Uživatelé a skupiny**vyberte **Možnost Všichni uživatelé**a potom vyberte Nový **uživatel**.
   
           The **User** pane opens:
      
      ![Podokno "Uživatel"](./media/add-user.png)
    
   1. Zadejte podrobnosti o uživateli, například **Jméno** a **Uživatelské jméno**. Část uživatelského jména s názvem název domény musí být buď počáteční výchozí název domény "[název domény].onmicrosoft.com" nebo ověřený, nefederovaný [vlastní název domény,](../../active-directory/add-custom-domain.md) například "contoso.com".
    
   1. Zkopírujte nebo si jiným způsobem poznamenejte vygenerované heslo uživatele, abyste ho mohli po dokončení tohoto procesu poskytnout příslušnému uživateli.
    
   1. Můžete ale také otevřít a vyplnit informace o uživateli v části **Profil**, **Skupiny** nebo **Role adresáře**. 
    
   1. V části **Uživatel**vyberte **Vytvořit**.
    
   1. Bezpečně distribuovat generované heslo pro nového uživatele, aby se mohli přihlásit.

1. Vytvořte instanci Azure AD DS. Postupujte podle pokynů v [části Povolení služby Azure Active Directory Domain Services pomocí portálu Azure (v](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) části Vytvoření instance a konfigurace základního nastavení). Je důležité aktualizovat existující uživatelská hesla ve službě Active Directory tak, aby se heslo ve službě Azure AD DS synchronizovalo. Je také důležité přidat DNS do Služby Azure AD DS, jak je popsáno v části "Vyplňte pole v okně Základy na webu Azure Portal k vytvoření instance Azure AD DS" v této části.

1. Vytvořte samostatnou podsíť DSVM ve virtuální síti vytvořené v části Vytvoření a konfigurace virtuální sítě v předchozím kroku.
1. Vytvořte jednu nebo více instancí DSVM v podsíti DSVM.
1. Podle [pokynů](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) přidejte dsvm do služby Active Directory. 
1. Připojení sdílené složky Azure Files k hostování vašeho domovského adresáře nebo adresáře poznámkového bloku, aby bylo možné pracovní prostor připojit na libovolném počítači. (Pokud potřebujete přísná oprávnění na úrovni souborů, budete potřebovat síťový systém souborů [NFS] spuštěný na jednom nebo více virtuálních počítačích.)

   1. [Vytvořte sdílenou složku souborů Azure](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Připojte tuto sdílenou složku na Linux DSVM. Když vyberete **Připojit** pro sdílené soubory Azure ve vašem účtu úložiště na webu Azure Portal, zobrazí se příkaz, který se má spustit v prostředí bash na Linuxu DSVM. Příkaz vypadá takto:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Předpokládejme například, že jste namontovali sdílené složky Azure Files v /data/workspace. Nyní vytvořte adresáře pro každého uživatele ve sdílené složce: /data/workspace/user1, /data/workspace/user2 a tak dále. Vytvořte `notebooks` adresář v pracovním prostoru každého uživatele. 
1. Vytvořte symbolické `notebooks` `$HOME/userx/notebooks/remote`odkazy pro v .   

Nyní máte uživatele v instanci služby Active Directory hostované v Azure. Pomocí přihlašovacích údajů služby Active Directory se uživatelé můžou přihlásit k libovolnému zařízení DSVM (SSH nebo JupyterHub), který je spojený se službou Azure AD DS. Vzhledem k tomu, že pracovní prostor uživatelů je ve sdílené složce Azure Files, mají uživatelé přístup ke svým poznámkovým blokům a další práci z libovolného dsvm, když používají JupyterHub.

Pro automatické škálování můžete použít škálovací sadu virtuálních počítačů k vytvoření fondu virtuálních počítačů, které jsou všechny připojené k doméně tímto způsobem a se sdíleným diskem připojeným. Uživatelé se můžou přihlásit k libovolnému dostupnému počítači ve škálovací sadě virtuálních počítačů a mít přístup ke sdílenému disku, kde jsou jejich poznámkové bloky uloženy. 

## <a name="next-steps"></a>Další kroky

* [Bezpečné ukládání přihlašovacích údajů pro přístup ke cloudovým prostředkům](dsvm-secure-access-keys.md)



