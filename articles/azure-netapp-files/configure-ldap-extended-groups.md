---
title: Konfigurace Přidání LDAP pomocí rozšířených skupin pro přístup ke svazku NFS Azure NetApp Files Microsoft Docs
description: V této části najdete popis kroků a postupu při povolování LDAP s rozšířenými skupinami při vytváření svazku NFS pomocí Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: 2031cbf07d700307ae1e11c516f9fc736bce5080
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498963"
---
# <a name="configure-adds-ldap-with-extended-groups-for-nfs-volume-access"></a>Konfigurace Přidání LDAP pomocí rozšířených skupin pro přístup ke svazkům NFS

Když [vytváříte svazek NFS](azure-netapp-files-create-volumes.md), máte možnost povolit funkci LDAP s rozšířenými skupinami (možnost **LDAP** ) pro daný svazek. Tato funkce povoluje přístup ke svazku uživatelům služby Active Directory LDAP a rozšířeným skupinám (až 1024 skupin).  

V tomto článku se dozvíte, jak postupovat při vytváření svazku NFS pomocí rozšířených skupin při povolování LDAP s rozšířenými skupinami.  

## <a name="considerations"></a>Požadavky

* Pokud používáte Azure Active Directory Domain Services (AADDS), *nesmí být protokol* LDAP over TLS povolený.  

* Pokud povolíte funkci LDAP s rozšířenými skupinami, [svazky protokolu Kerberos](configure-kerberos-encryption.md) s podporou protokolu LDAP nebudou správně zobrazovat vlastnictví souborů pro uživatele LDAP. Soubor nebo adresář vytvořený uživatelem protokolu LDAP bude `root` jako vlastník místo skutečného uživatele protokolu LDAP výchozí. `root`Účet však může ručně změnit vlastnictví souboru pomocí příkazu `chown <username> <filename>` . 

* Po vytvoření svazku nemůžete změnit nastavení možnosti LDAP (povoleno nebo zakázáno).  

* Následující tabulka popisuje nastavení TTL (Time to Live) pro mezipaměť LDAP. Než se pokusíte o přístup k souboru nebo adresáři přes klienta, musíte počkat, než se aktualizuje mezipaměť. V opačném případě se v klientovi zobrazí zpráva o odepření přístupu. 

    |     Chybový stav    |     Řešení    |
    |-|-|
    | Mezipaměť |  Výchozí časový limit |
    | Seznam členství ve skupině  | 24hodinový čas TTL  |
    | Skupiny systému UNIX  | 24hodinový čas TTL, záporná hodnota TTL od 1 minuty  |
    | Uživatelé systému UNIX  | 24hodinový čas TTL, záporná hodnota TTL od 1 minuty  |

    Mezipaměti mají určité časové období *s názvem Time to Live*. Po uplynutí časového limitu vyprší platnost položek tak, aby zastaralé položky nestály. *Záporná hodnota TTL* je tam, kde vyhledávání, které se nezdařilo, se nezdařilo, aby se zabránilo problémům s výkonem v důsledku dotazů protokolu LDAP pro objekty, které pravděpodobně neexistují. "        

## <a name="steps"></a>Postup

1. Funkce LDAP s rozšířenými skupinami je momentálně ve verzi Preview. Před prvním použitím této funkce je potřeba tuto funkci zaregistrovat:  

    1. Zaregistrujte funkci:   

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```

    2. Ověřte stav registrace funkce: 

        > [!NOTE]
        > **RegistrationState** může být ve `Registering` stavu až 60 minut, než se změní na `Registered` . Než budete pokračovat, počkejte na stav `Registered` .

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```
        
    Můžete také použít [příkazy rozhraní příkazového řádku Azure](/cli/azure/feature) `az feature register` a `az feature show` zaregistrovat funkci a zobrazit stav registrace. 

2. Svazky LDAP vyžadují konfiguraci služby Active Directory pro nastavení serveru LDAP. Postupujte podle pokynů v části [požadavky na připojení ke službě Active Directory](create-active-directory-connections.md#requirements-for-active-directory-connections) a [vytvořte připojení](create-active-directory-connections.md#create-an-active-directory-connection) ke službě Active Directory, abyste mohli konfigurovat připojení služby Active Directory na Azure Portal.  

3. Zajistěte, aby byl server služby Active Directory LDAP v provozu a běžel ve službě Active Directory. Můžete to udělat tak, že nainstalujete a nakonfigurujete roli [Služba AD LDS (Active Directory Lightweight Directory Services) (AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) na počítači AD.

4. Uživatelé systému souborů NFS NFS musí mít na serveru LDAP určité atributy POSIX. Pro nastavení požadovaných atributů použijte postup pro [správu atributů LDAP POSIX](create-volumes-dual-protocol.md#manage-ldap-posix-attributes) .  

5. Pokud chcete nakonfigurovat klienta Linux s integrovaným protokolem LDAP, přečtěte si téma [Konfigurace klienta NFS pro Azure NetApp Files](configure-nfs-clients.md).

6.  Pokud chcete vytvořit svazek NFS, postupujte podle kroků v částech [vytvoření svazku NFS pro Azure NetApp Files](azure-netapp-files-create-volumes.md) . Během procesu vytváření svazku povolte na kartě **protokol** možnost **LDAP** .   

    ![Snímek obrazovky, který ukazuje stránku vytvořit svazek s možností LDAP.](../media/azure-netapp-files/create-nfs-ldap.png)  

7. Volitelné – pro přístup ke svazku NFS s povoleným rozšířenými skupinami můžete povolit uživatele místního klienta NFS, kteří nejsou k dispozici na serveru Windows LDAP. Provedete to tak, že povolíte možnost **Povolit místní uživatele NFS s protokolem LDAP** následujícím způsobem:
    1. Klikněte na **připojení služby Active Directory**.  V existujícím připojení služby Active Directory klikněte na místní nabídku (tři tečky `…` ) a vyberte **Upravit**.  
    2. V okně **Upravit nastavení služby Active Directory** , které se zobrazí, vyberte možnost **Povolení místních uživatelů NFS s protokolem LDAP** .  

    ![Snímek obrazovky zobrazující možnost povolení místních uživatelů systému souborů NFS s protokolem LDAP](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  

## <a name="next-steps"></a>Další kroky  

* [Vytvoření svazku NFS pro službu Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Vytváření a Správa připojení ke službě Active Directory](create-active-directory-connections.md)
* [Řešení potíží se svazkem LDAP](troubleshoot-ldap-volumes.md)
