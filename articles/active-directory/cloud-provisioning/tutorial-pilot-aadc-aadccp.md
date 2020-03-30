---
title: Kurz – pilotní zřizování cloudu Azure AD Connect pro existující synchronizovanou doménovou strukturu služby AD
description: Tutorial.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aba42e6bd9b11e47d793219c0ff06b9177d609f5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78298815"
---
# <a name="pilot-cloud-provisioning-for-an-existing-synced-ad-forest"></a>Zřízení pilotního cloudu pro existující synchronizovanou doménovou strukturu AD 

Tento kurz vás provede pilotním zřizováním cloudu pro testovací doménovou strukturu služby Active Directory, která je už synchronizovaná pomocí synchronizace Azure Active Directory (Azure AD).

![Vytvořit](media/tutorial-migrate-aadc-aadccp/diagram.png)

## <a name="considerations"></a>Požadavky
Než se pokusíte tento kurz, zvažte následující položky:
1. Ujistěte se, že jste obeznámeni se základy zřizování cloudu. 
2. Ujistěte se, že používáte Azure AD Connect synchronizační verze 1.4.32.0 nebo novější a nakonfigurovali pravidla synchronizace jako zdokumentovaná. Při pilotním testování odeberete testovací ou nebo skupinu z oboru synchronizace Azure AD Connect. Přesunutí objektů mimo rozsah vede k odstranění těchto objektů ve službě Azure AD. V případě uživatelských objektů jsou objekty ve službě Azure AD obnovitelné a lze je obnovit. V případě objektů skupiny objekty ve službě Azure AD jsou pevně odstraněna a nelze obnovit. Nový typ propojení byla zavedena v synchronizaci Azure AD Connect, která zabrání odstranění v případě pilotního scénáře. 
3. Ujistěte se, že objekty v pilotním oboru mají ms-ds-consistencyGUID naplněný tak, aby zřizování cloudu pevné odpovídá objektům. 

   > [!NOTE]
   > Azure AD Connect synchronizace nenaplní *ms-ds konzistenceGUID* ve výchozím nastavení pro objekty skupiny.

4. Toto je pokročilý scénář. Ujistěte se, že postupujte podle kroků zdokumentovaných v tomto kurzu přesně.

## <a name="prerequisites"></a>Požadavky
Níže jsou uvedeny požadavky potřebné pro dokončení tohoto kurzu
- Testovací prostředí se synchronizátorovou verzí Azure AD Connect 1.4.32.0 nebo novější
- Ob ovou nebo skupinu, která je v rozsahu synchronizace a lze použít pilotní. Doporučujeme začít s malou sadou objektů.
- Server se systémem Windows Server 2012 R2 nebo novějším, který bude hostitelem agenta zřizování.  To nemůže být stejný server jako server Azure AD Connect.
- Zdrojová kotva pro synchronizaci připojení AAD by měla být *objektGuid* nebo *ms-ds-consistencyGUID*

## <a name="update-azure-ad-connect"></a>Aktualizace služby Azure AD Connect

Minimálně byste měli mít [Azure AD connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.4.32.0. Chcete-li aktualizovat synchronizaci Azure AD Connect, proveďte kroky v [Azure AD Connect: Upgrade na nejnovější verzi](../hybrid/how-to-upgrade-previous-version.md).  

## <a name="stop-the-scheduler"></a>Zastavení plánovače
Synchronizace azure ad připojení synchronizuje změny, ke kterým dochází v místním adresáři pomocí plánovače. Chcete-li upravit a přidat vlastní pravidla, chcete zakázat plánovač tak, aby synchronizace nebude spuštěna při práci na tomto.  Použijte k tomu následující postup:

1.  Na serveru, na který běží Azure AD Connect, synchronizujte open PowerShell s oprávněními správce.
2.  Spusťte `Stop-ADSyncSyncCycle`.  Stiskněte klávesu Enter.
3.  Spusťte `Set-ADSyncScheduler -SyncCycleEnabled $false`.

>[!NOTE] 
>Pokud používáte vlastní plánovač pro synchronizaci AAD Connect, zakažte plánovač. 

## <a name="create-custom-user-inbound-rule"></a>Vytvořit vlastní uživatelské příchozí pravidlo

 1. Spusťte editor synchronizace z nabídky aplikace na ploše, jak je znázorněno níže:</br>
 ![Nabídka Editoru pravidel synchronizace](media/how-to-cloud-custom-user-rule/user8.png)</br>
 
 2. V rozevíracím seznamu Směr vyberte **Příchozí** a klikněte na **Přidat nové pravidlo**.
 ![Vlastní pravidlo](media/how-to-cloud-custom-user-rule/user1.png)</br>
 
 3. Na stránce **Popis** zadejte následující a klepněte na **tlačítko Další**:

    **Název:** Pojmenujte pravidlu smysluplný název<br>
    **Popis:** Přidání smysluplného popisu<br>
    **Připojený systém:** Zvolte konektor služby AD, pro který píšete vlastní pravidlo synchronizace.<br>
    **Typ připojeného systémového objektu:** Uživatele<br>
    **Typ objektu Metaverse:** Osoba<br>
    **Typ odkazu:** Připojit<br>
    **Priorita:** Zadejte hodnotu, která je v systému jedinečná.<br>
    **Značka:** Nechte to prázdné<br>
    ![Vlastní pravidlo](media/how-to-cloud-custom-user-rule/user2.png)</br>
 
 4. Na stránce **filtru oborů** zadejte ou nebo skupinu zabezpečení, kterou má pilotní objekt založený na pilotním projektu vypnout.  Chcete-li filtrovat podle ou, přidejte část rozlišujícího názvu. Toto pravidlo bude použito pro všechny uživatele, kteří jsou v této řadě.  Takže pokud DN končí "OU = CPUsers,DC = contoso, DC = com, byste přidat tento filtr.  Pak klikněte na **Další**. 

    |Pravidlo|Atribut|Operátor|Hodnota|
    |-----|----|----|-----|
    |Ouoborování|Dn|ENDSWITH|Rozlišující název ou.|
    |Skupina oboru||ISČLEN|Rozlišující název skupiny zabezpečení.|

    ![Vlastní pravidlo](media/how-to-cloud-custom-user-rule/user3.png)</br>
 
 5. Na stránce **Pravidla spojení** klikněte na **další**.
 6. Na stránce **Transformace** přidejte konstantní transformace: tok True do cloudNoFlow atribut. Klikněte na **Přidat**.
 ![Vlastní pravidlo](media/how-to-cloud-custom-user-rule/user4.png)</br>

Stejné kroky je třeba dodržovat pro všechny typy objektů (uživatel, skupina a kontakt). Opakujte kroky na nakonfigurovaný konektor služby AD / v doménové struktuře služby AD. 

## <a name="create-custom-user-outbound-rule"></a>Vytvořit vlastní odchozí pravidlo uživatele

 1. V rozevíracím seznamu Směr vyberte **Odchozí** a klikněte na **Přidat pravidlo**.
 ![Vlastní pravidlo](media/how-to-cloud-custom-user-rule/user5.png)</br>
 
 2. Na stránce **Popis** zadejte následující a klepněte na **tlačítko Další**:

    **Název:** Pojmenujte pravidlu smysluplný název<br>
    **Popis:** Přidání smysluplného popisu<br>
    **Připojený systém:** Zvolte konektor AAD, pro který píšete vlastní pravidlo synchronizace.<br>
    **Typ připojeného systémového objektu:** Uživatele<br>
    **Typ objektu Metaverse:** Osoba<br>
    **Typ odkazu:** JoinNoFlow<br>
    **Priorita:** Zadejte hodnotu, která je v systému jedinečná.<br>
    **Značka:** Nechte to prázdné<br>
    
    ![Vlastní pravidlo](media/how-to-cloud-custom-user-rule/user6.png)</br>
 
 3. Na stránce **filtru oborů** zvolte **cloudNoFlow** equal **True**. Pak klikněte na **Další**.
 ![Vlastní pravidlo](media/how-to-cloud-custom-user-rule/user7.png)</br>
 
 4. Na stránce **Pravidla spojení** klikněte na **další**.
 5. Na stránce **Transformace** klikněte na **Přidat**.

Stejné kroky je třeba dodržovat pro všechny typy objektů (uživatel, skupina a kontakt).

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Instalace agenta zřizování Azure AD Connect
1. Přihlaste se k serveru, který budete používat s oprávněními správce rozlehlé sítě.  Pokud používáte [základní a disponuje a azure prostředí](tutorial-basic-ad-azure.md) kurz by CP1.
2. Stáhněte si agenta azure ad připojení cloudu zřizování pomocí kroků popsaných [zde](how-to-install.md#install-the-agent).
3. Spuštění zřizování cloudu Azure AD Connect (AADConnectProvisioningAgent.Installer)
3. Na úvodní obrazovce **přijměte** licenční podmínky a klepněte na tlačítko **Instalovat**.</br>
![Obrazovka Vítejte](media/how-to-install/install1.png)</br>

4. Po dokončení této operace se spustí průvodce konfigurací.  Přihlaste se pomocí účtu globálního správce Azure AD.
5. Na obrazovce **Připojit službu Active Directory** klikněte na Přidat **adresář** a přihlaste se pomocí účtu správce služby Active Directory.  Tato operace přidá místní adresář.  Klikněte na **Další**.</br>
![Obrazovka Vítejte](media/how-to-install/install3.png)</br>

6. Na obrazovce **Dokončení konfigurace** klepněte na **tlačítko Potvrdit**.  Tato operace zaregistruje a restartuje agenta.</br>
![Obrazovka Vítejte](media/how-to-install/install4.png)</br>

7. Po dokončení této operace byste měli vidět oznámení **Vaše byla úspěšně ověřena.**  Klepněte na tlačítko **Ukončit**.</br>
![Obrazovka Vítejte](media/how-to-install/install5.png)</br>
8. Pokud se stále zobrazí úvodní úvodní obrazovka, klepněte na **tlačítko Zavřít**.

## <a name="verify-agent-installation"></a>Ověření instalace agenta
K ověření agenta dochází na portálu Azure a na místním serveru, na který agenta běží.

### <a name="azure-portal-agent-verification"></a>Ověření agenta portálu Azure
Chcete-li ověřit agenta se zoažená v Azure postupujte takto:

1. Přihlaste se k portálu Azure.
2. Na levé straně vyberte **Azure Active Directory**, klikněte na Azure **AD Connect** a na střed vyberte Spravovat **zřizování (preview).**</br>
![Azure Portal](media/how-to-install/install6.png)</br>

3.  Na obrazovce **Azure AD Provisioning (preview)** klikněte na **Zkontrolovat všechny agenty**.
![Zřizování Azure AD](media/how-to-install/install7.png)</br>
 
4. Na **obrazovce Místní zřizovací agenti** uvidíte agenty, které jste nainstalovali.  Ověřte, zda je dotyčný agent k dispozici a zda je označen **jako Zakázaný**.  Agent je ve ![výchozím nastavení zakázán agenty zřizování.](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>Na místním serveru
Chcete-li ověřit, zda je agent spuštěn, postupujte takto:

1.  Přihlášení k serveru pomocí účtu správce
2.  Spusťte **služby** buď navigací na něj, nebo přechodem na Start/Run/Services.msc.
3.  V části **Služby** se ujistěte, že jsou tam **Microsoft Azure AD Connect Agent Updater** a **Microsoft Azure AD Connect Provisioning Agent** a že je **spuštěný**stav .
![Služby](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Konfigurace zřizování cloudu Azure AD Connect
Pomocí následujících kroků nakonfigurujte zřizování:

 1. Přihlaste se k portálu Azure AD.
 2. Klikněte na **Službu Active Directory služby Azure.**
 3. Klikněte na **Azure AD Connect**
 4. Vyberte **Spravovat zřizování (náhled)**
 ![](media/how-to-configure/manage1.png)</br>
 5.  Klikněte na **Nová konfigurace.**
 ![](media/tutorial-single-forest/configure1.png)</br>
 6.  Na konfigurační obrazovce zadejte **e-mail s oznámením**, přesuňte volič na **Povolit** a klepněte na **tlačítko Uložit**.
 ![](media/tutorial-single-forest/configure2.png)</br>
 7. V části **Konfigurovat**vyberte **Možnost Všichni uživatelé,** chcete-li změnit rozsah pravidla konfigurace.
 ![](media/how-to-configure/scope2.png)</br>
 8. Na pravé straně změňte obor tak, aby zahrnoval konkrétní ou, kterou jste právě vytvořili "OU=CPUsers,DC=contoso,DC=com".
 ![](media/tutorial-existing-forest/scope2.png)</br>
 9.  Klepněte na **tlačítko Hotovo** a **Uložit**.
 10. Obor by nyní měl být nastaven na jednu organizační jednotku. 
 ![](media/tutorial-existing-forest/scope3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-provisioning"></a>Ověření, že uživatelé jsou zřaženi zřizováním cloudu
Teď ověříte, že uživatelé, které jste měli v našem místním adresáři, byli synchronizováni a teď existují v tenantovi Azure AD.  Uvědomte si, že to může trvat několik hodin.  Chcete-li ověřit, že uživatelé zřazují pomocí zřizování cloudu, postupujte takto:

1. Přejděte na [Azure Portal](https://portal.azure.com) a přihlaste se pomocí účtu, který má předplatné Azure.
2. Na levé straně vyberte **Azure Active Directory.**
3. Klikněte na **Azure AD Connect**
4. Klikněte na **Spravovat zřizování (náhled)**
5. Klikněte na tlačítko **Protokoly**
6. Vyhledejte uživatelské jméno a ověřte, že uživatel je zřízen zřizováním cloudu.

Kromě toho můžete ověřit, že uživatel a skupina existují ve službě Azure AD.

## <a name="start-the-scheduler"></a>Spuštění plánovače
Synchronizace azure ad připojení synchronizuje změny, ke kterým dochází v místním adresáři pomocí plánovače. Nyní, když jste změnili pravidla, můžete znovu spustit plánovač.  Použijte k tomu následující postup:

1.  Na serveru se spuštěným azure ad připojit synchronizace open PowerShell s oprávněními správce
2.  Spusťte `Set-ADSyncScheduler -SyncCycleEnabled $true`.
3.  Spusťte `Start-ADSyncSyncCycle`.  Stiskněte klávesu Enter.  

>[!NOTE] 
>Pokud používáte vlastní plánovač pro synchronizaci AAD Connect, povolte plánovač. 

## <a name="something-went-wrong"></a>Něco se pokazilo.
V případě, že pilotní nefunguje podle očekávání, můžete se vrátit k nastavení synchronizace Azure AD Connect podle následujících kroků:
1.  Zakažte zřizování konfigurace na webu Azure Portal. 
2.  Zakažte všechna vlastní pravidla synchronizace vytvořená pro zřizování cloudu pomocí nástroje Editor pravidel synchronizace. Zakázání by mělo způsobit úplnou synchronizaci na všech konektorech.

## <a name="configure-azure-ad-connect-sync-to-exclude-the-pilot-ou"></a>Konfigurace synchronizace služby Azure AD Connect pro vyloučení pilotní ou.
Jakmile ověříte, že uživatelé z pilotní ou jsou úspěšně spravovány zřizování cloudu, můžete znovu nakonfigurovat Azure AD Connect vyloučit pilotní OU, která byla vytvořena výše.  Agent zřizování cloudu bude zpracovávat synchronizace pro tyto uživatele do budoucna.  Pomocí následujících kroků k oboru Azure AD Connect.

 1. Na serveru, na který běží Azure AD Connect, poklikejte na ikonu Azure AD Connect.
 2. Klikněte na **Konfigurovat**
 3. Vyberte **Přizpůsobit možnosti synchronizace** a klepněte na další.
 4. Přihlaste se k Azure AD a klikněte na **další**.
 5. Na obrazovce **Připojit adresáře** klepněte na **tlačítko Další**.
 6. Na obrazovce **filtrování domény a ou** vyberte **synchronizovat vybrané domény a oupoložky**.
 7. Rozbalte svou doménu a **zrušte výběr** OU **uživatelů CPUsers.**  Klikněte na **Další**.
![Rozsah](media/tutorial-existing-forest/scope1.png)</br>
 9. Na obrazovce **Volitelné funkce** klepněte na tlačítko **Další**.
 10. Na obrazovce **Připraveno ke konfiguraci** klepněte na **tlačítko Konfigurovat**.
 11. Po dokončení klepněte na tlačítko **Ukončit**. 

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)

