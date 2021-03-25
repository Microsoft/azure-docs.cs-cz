---
title: Kurz – pilotní Azure AD Connect cloudová synchronizace pro existující synchronizovanou doménovou strukturu AD
description: Naučte se pilotní synchronizaci cloudu pro testovací doménovou strukturu Active Directory, která je už synchronizovaná pomocí služby Azure Active Directory (Azure AD) Connect Sync.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5053bd0c3d63b13b1021476a09dca39dd08f581
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108753"
---
# <a name="pilot-cloud-sync-for-an-existing-synced-ad-forest"></a>Pilotní cloudová synchronizace pro existující synchronizovanou doménovou strukturu AD 

Tento kurz vás provede pilotní synchronizací cloudu pro testovací doménovou strukturu Active Directory, která je už synchronizovaná pomocí služby Azure Active Directory (Azure AD) Connect Sync.

![Vytvořit](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

## <a name="considerations"></a>Požadavky
Než si vyzkoušíte tento kurz, vezměte v úvahu následující položky:
1. Ujistěte se, že jste obeznámeni se základními informacemi o cloudové synchronizaci. 
2. Ujistěte se, že používáte Azure AD Connect Sync verze 1.4.32.0 nebo novější a že jste nakonfigurovali pravidla synchronizace podle zdokumentovaného postupu. Při pilotním nasazení budete z rozsahu Azure AD Connect synchronizace odebírat testovací organizační jednotku nebo skupinu. Přesunutí objektů z oboru vede k odstranění těchto objektů ve službě Azure AD. V případě uživatelských objektů se objekty ve službě Azure AD odpouštějí a dají se obnovit. V případě objektů skupin se objekty ve službě Azure AD neodstraní a nelze je obnovit. V Azure AD Connect synchronizaci se zavedl nový typ odkazu, který zabrání odstranění v případě pilotního scénáře. 
3. Zajistěte, aby objekty v pilotním oboru měly consistencyGUIDy ms-DS-to, aby cloudová synchronizace odpovídala objektům. 

   > [!NOTE]
   > Azure AD Connect Sync neplní ve výchozím nastavení pro objekty skupin skupinu *MS-DS-consistencyGUID* .

4. Toto je pokročilý scénář. Ujistěte se, že postup je přesně popsaný v tomto kurzu.

## <a name="prerequisites"></a>Požadavky
Níže jsou uvedené předpoklady nezbytné pro dokončení tohoto kurzu.
- Testovací prostředí s Azure AD Connect synchronizace verze 1.4.32.0 nebo novější
- Organizační jednotka nebo skupina, která je v rozsahu synchronizace a kterou lze použít pro pilotní nasazení. Doporučujeme začít s malou sadou objektů.
- Server se systémem Windows Server 2012 R2 nebo novějším, který bude hostovat agenta zřizování.
- Zdrojová kotva pro Azure AD Connect Sync by měla být buď *objectGUID* , nebo *MS-DS-consistencyGUID* .

## <a name="update-azure-ad-connect"></a>Aktualizovat Azure AD Connect

Jako minimum byste měli mít [službu Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.4.32.0. Chcete-li aktualizovat Azure AD Connect synchronizaci, proveďte kroky v [Azure AD Connect: upgrade na nejnovější verzi](../hybrid/how-to-upgrade-previous-version.md).  

## <a name="stop-the-scheduler"></a>Zastavení plánovače
Azure AD Connect synchronizace synchronizuje změny, ke kterým došlo v místním adresáři, pomocí plánovače. Chcete-li upravit a přidat vlastní pravidla, je třeba zakázat Plánovač, aby při práci na tomto procesu neběžely synchronizace.  Použijte k tomu následující postup:

1.  Na serveru se spuštěným Azure AD Connect synchronizace otevřete PowerShell s oprávněními správce.
2.  Spusťte `Stop-ADSyncSyncCycle`.  Stiskněte ENTER.
3.  Spusťte `Set-ADSyncScheduler -SyncCycleEnabled $false`.

>[!NOTE] 
>Pokud používáte vlastní Plánovač pro Azure AD Connect synchronizaci, zakažte prosím Plánovač. 

## <a name="create-custom-user-inbound-rule"></a>Vytvořit vlastní příchozí pravidlo pro uživatele

 1. Spusťte Editor synchronizace v nabídce aplikace na ploše, jak je znázorněno níže:</br>
 ![Nabídka editoru synchronizačního pravidla](media/tutorial-migrate-aadc-aadccp/user-8.png)</br>
 
 2. V rozevíracím seznamu vyberte **příchozí** a klikněte na **Přidat nové pravidlo**.
 ![Snímek obrazovky s oknem zobrazit a spravovat vaše pravidla synchronizace s možností příchozí a vybrané tlačítko Přidat nové pravidlo](media/tutorial-migrate-aadc-aadccp/user-1.png)</br>
 
 3. Na stránce **Popis** zadejte následující text a klikněte na tlačítko **Další**:

    **Název:** Udělení pravidla smysluplnému názvu<br>
    **Popis:** Přidat smysluplný popis<br>
    **Připojený systém:** Vyberte konektor služby Active Directory, pro který píšete vlastní pravidlo synchronizace.<br>
    **Typ připojeného systémového objektu:** Uživatelský<br>
    **Typ objektu úložiště metaverse:** Uživateli<br>
    **Typ odkazu:** Zúčastnit<br>
    **Priorita:** Zadejte hodnotu, která je v systému jedinečná.<br>
    **Značka:** Ponechat toto prázdné<br>
    ![Snímek obrazovky zobrazující stránku vytvořit příchozí synchronizaci pravidla – popis s zadanými hodnotami](media/tutorial-migrate-aadc-aadccp/user-2.png)</br>
 
 4. Na stránce **Filtr oboru** zadejte organizační jednotku nebo skupinu zabezpečení, na které se má pilotní nasazení vycházet.  Pokud chcete filtrovat podle organizační jednotky, přidejte část s názvem organizační jednotky rozlišujícího názvu. Toto pravidlo bude použito pro všechny uživatele, kteří jsou v dané organizační jednotce.  Pokud tedy DN končí na "OU = procesory, DC = contoso, DC = com, přidáte tento filtr.  Potom klikněte na **Další**. 

    |Pravidlo|Atribut|Operátor|Hodnota|
    |-----|----|----|-----|
    |Určení oboru organizační jednotky|JMÉNA|ENDSWITH|Rozlišující název organizační jednotky|
    |Rozsah skupiny||-MEMBEROF|Rozlišující název skupiny zabezpečení|

    ![Snímek obrazovky se stránkou vytvořit příchozí synchronizační pravidlo – filtr rozsahu s zadanou hodnotou filtru oborů](media/tutorial-migrate-aadc-aadccp/user-3.png)</br>
 
 5. Na stránce pravidla **spojování** klikněte na **Další**.
 6. Na stránce **transformace** přidejte konstantní transformaci: Flow true do atributu cloudNoFlow. Klikněte na **Přidat**.
 ![Snímek obrazovky s přidaným stránkou "Vytvoření příchozího synchronizačního pravidla – transformace" s přidaným tokem "konstantní transformace".](media/tutorial-migrate-aadc-aadccp/user-4.png)</br>

Pro všechny typy objektů (uživatel, skupina a kontakt) se musí provést stejný postup. Opakujte kroky podle nakonfigurované doménové struktury AD Connector/AD na jednu. 

## <a name="create-custom-user-outbound-rule"></a>Vytvořit vlastní pravidlo odchozího uživatele

 1. Zvolte **odchozí** z rozevíracího seznamu pro směr a klikněte na **Přidat pravidlo**.
 ![Snímek obrazovky zobrazující vybraný směr "odchozího" a zvýrazněno tlačítko Přidat nové pravidlo.](media/tutorial-migrate-aadc-aadccp/user-5.png)</br>
 
 2. Na stránce **Popis** zadejte následující text a klikněte na tlačítko **Další**:

    **Název:** Udělení pravidla smysluplnému názvu<br>
    **Popis:** Přidat smysluplný popis<br>
    **Připojený systém:** Vyberte konektor Azure AD, pro který píšete vlastní pravidlo synchronizace.<br>
    **Typ připojeného systémového objektu:** Uživatelský<br>
    **Typ objektu úložiště metaverse:** Uživateli<br>
    **Typ odkazu:** JoinNoFlow<br>
    **Priorita:** Zadejte hodnotu, která je v systému jedinečná.<br>
    **Značka:** Ponechat toto prázdné<br>
    
    ![Snímek obrazovky zobrazující stránku popisu s zadanými vlastnostmi](media/tutorial-migrate-aadc-aadccp/user-6.png)</br>
 
 3. Na stránce **Filtr oboru** vyberte **CloudNoFlow** rovná se **true**. Potom klikněte na **Další**.
 ![Vlastní pravidlo](media/tutorial-migrate-aadc-aadccp/user-7.png)</br>
 
 4. Na stránce pravidla **spojování** klikněte na **Další**.
 5. Na stránce **transformace** klikněte na tlačítko **Přidat**.

Pro všechny typy objektů (uživatel, skupina a kontakt) se musí provést stejný postup.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Instalace agenta pro zřizování Azure AD Connect
1. Přihlaste se k serveru, který budete používat s oprávněními podnikového správce.  Pokud používáte  [Základní kurz prostředí AD a prostředí Azure](tutorial-basic-ad-azure.md) , bude CP1.
2. Stáhněte si tohoto Azure AD Connectho agenta zřizování cloudu pomocí kroků uvedených [tady](how-to-install.md#install-the-agent).
3. Spuštění synchronizace Azure AD Connect cloudu (AADConnectProvisioningAgent. Installer)
3. Na úvodní obrazovce **přijměte** licenční podmínky a klikněte na **nainstalovat**.</br>
![Snímek obrazovky s úvodní obrazovkou "Microsoft Azure A D Connect zřizovací agent.](media/how-to-install/install-1.png)</br>

4. Po dokončení této operace se spustí Průvodce konfigurací nástroje.  Přihlaste se pomocí účtu globálního správce služby Azure AD.
5. Na obrazovce **připojit ke službě Active Directory** klikněte na **Přidat adresář** a pak se přihlaste pomocí účtu správce služby Active Directory.  Tato operace přidá váš místní adresář.  Klikněte na **Next** (Další).</br>
![Snímek obrazovky, který zobrazuje obrazovku "připojit službu Active Directory" se zadanou hodnotou adresáře.](media/how-to-install/install-3a.png)</br>

6. Na obrazovce **Konfigurace byla dokončena** klikněte na **Potvrdit**.  Tato operace provede registraci a restart agenta.</br>
![Snímek obrazovky zobrazující obrazovku "konfigurace dokončena" s vybraným tlačítkem potvrdit.](media/how-to-install/install-4a.png)</br>

7. Po dokončení této operace by se měla zobrazit oznámení, že **vaše ověření bylo úspěšné.**  Můžete kliknout na tlačítko **konec**.</br>
![Obrazovka Vítejte](media/how-to-install/install-5.png)</br>
8. Pokud se stále zobrazuje úvodní úvodní obrazovka, klikněte na **Zavřít**.

## <a name="verify-agent-installation"></a>Ověřit instalaci agenta
K ověření agenta dochází v Azure Portal a na místním serveru, na kterém je spuštěný agent.

### <a name="azure-portal-agent-verification"></a>Ověřování agenta Azure Portal
Pokud chcete ověřit, že se agent zobrazuje v Azure, postupujte podle těchto kroků:

1. Přihlaste se k webu Azure Portal.
2. Na levé straně vyberte **Azure Active Directory**, klikněte na **Azure AD Connect** a ve středu vyberte **spravovat synchronizaci cloudu**.</br>
![Azure Portal](media/how-to-install/install-6.png)</br>

3.  Na obrazovce **Azure AD Connect synchronizace cloudu** klikněte na **zkontrolovat všechny agenty**.
![Zřizování Azure AD](media/how-to-install/install-7.png)</br>
 
4. Na **obrazovce místní zřizovací agenti** se zobrazí agenti, které jste nainstalovali.  Ověřte, že je na něm daný agent a že je označený jako **zakázaný**.  Agent je ve výchozím nastavení zakázaný. ![](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>Na místním serveru
Pokud chcete ověřit, jestli je agent spuštěný, postupujte takto:

1.  Přihlaste se k serveru pomocí účtu správce.
2.  Otevřete **služby** tak, že k němu přejdete nebo přejdete na Start/Run/Services. msc.
3.  V části **služby** se ujistěte, že **Microsoft Azure AD připojit** nástroj aktualizace agenta a že je spuštěný **Agent Microsoft Azure AD připojení zřizování** a že je ve stavu **spuštěno**.
![Služby](media/how-to-install/troubleshoot-1.png)

## <a name="configure-azure-ad-connect-cloud-sync"></a>Konfigurace Azure AD Connect synchronizace cloudu
Při konfiguraci zřizování použijte následující postup:

 1. Přihlaste se k portálu Azure AD.
 2. Klikněte na **Azure Active Directory**
 3. Klikněte na **Azure AD Connect**
 4. Vyberte spravovat snímek obrazovky pro **synchronizaci cloudu** 
  ![ , na kterém se zobrazuje odkaz Spravovat cloudovou synchronizaci.](media/how-to-configure/manage-1.png)</br>
 5.  Klikněte na **Nová konfigurace** 
  ![ obrazovky Azure AD Connect obrazovka synchronizace cloudu s zvýrazněným odkazem na novou konfiguraci.](media/tutorial-single-forest/configure-1.png)</br>
 6.  Na obrazovce konfigurace zadejte **e-mailové oznámení**, přesuňte selektor, který chcete **Povolit** , a klikněte na **Uložit**.
 ![Obrazovka Konfigurace obrazovky s vyplněným e-mailem oznámení a vybraným povolením](media/tutorial-single-forest/configure-2.png)</br>
 7. V části **Konfigurovat** vyberte možnost **Všichni uživatelé** a změňte rozsah pravidla konfigurace.
 ![Snímek obrazovky s konfigurací "Všichni uživatelé", který je zvýrazněný vedle možnosti "uživatelé oboru".](media/how-to-configure/scope-2.png)</br>
 8. Napravo změňte obor tak, aby zahrnoval konkrétní organizační jednotku, kterou jste právě vytvořili "OU = procesory, DC = contoso, DC = com".
 ![Snímek obrazovky s oborem, ve kterém se zvýrazní rozsah, který se změnil na organizační jednotku, kterou jste vytvořili.](media/tutorial-existing-forest/scope-2.png)</br>
 9.  Klikněte na **Hotovo** a **Uložit**.
 10. Rozsah by teď měl být nastavený na jednu organizační jednotku. 
 ![Snímek obrazovky s konfigurací "1 organizační jednotka" označená vedle možnosti "uživatelé oboru".](media/tutorial-existing-forest/scope-3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-sync"></a>Ověřit, jestli jsou uživatelé zřízené cloudovou synchronizací
Nyní ověříte, že uživatelé, kteří byli v místním adresáři, byli synchronizováni a nyní existují v tenantovi Azure AD.  Mějte na paměti, že dokončení tohoto může trvat několik hodin.  Pokud chcete ověřit, jestli jsou uživatelé zřizování cloudovou synchronizací, postupujte takto:

1. Přejděte na [Azure Portal](https://portal.azure.com) a přihlaste se pomocí účtu, který má předplatné Azure.
2. Na levé straně vyberte **Azure Active Directory**
3. Klikněte na **Azure AD Connect**
4. Klikněte na **Spravovat cloudovou synchronizaci** .
5. Klikněte na tlačítko **protokoly** .
6. Vyhledejte uživatelské jméno a potvrďte, že je uživatel zřízený cloudovou synchronizací.

Navíc můžete ověřit, jestli uživatel a skupina existují ve službě Azure AD.

## <a name="start-the-scheduler"></a>Spustit Plánovač
Azure AD Connect synchronizace synchronizuje změny, ke kterým došlo v místním adresáři, pomocí plánovače. Teď, když jste změnili pravidla, můžete znovu spustit Plánovač.  Použijte k tomu následující postup:

1.  Na serveru se spuštěným Azure AD Connect synchronizace otevřete PowerShell s oprávněními správce.
2.  Spusťte `Set-ADSyncScheduler -SyncCycleEnabled $true`.
3.  Spusťte `Start-ADSyncSyncCycle`.  Stiskněte ENTER.  

>[!NOTE] 
>Pokud používáte vlastní Plánovač pro Azure AD Connect synchronizaci, povolte prosím Plánovač. 

Jakmile je Plánovač povolený, Azure AD Connect zastaví export jakýchkoli změn objektů `cloudNoFlow=true` do úložiště metaverse, pokud žádný atribut Reference (např. manažer) se aktualizuje. V případě, že je u objektu nějaká aktualizace atributu reference, Azure AD Connect bude signál ignorovat `cloudNoFlow` a exportovat všechny aktualizace objektu.

## <a name="something-went-wrong"></a>Něco se pokazilo.
V případě, že pilotní projekt nefunguje podle očekávání, můžete se vrátit k nastavení Azure AD Connect synchronizace pomocí následujících kroků:
1.  Zakažte konfiguraci zřizování v Azure Portal. 
2.  Pomocí nástroje Editor pravidel synchronizace zakažte všechna vlastní pravidla synchronizace vytvořená pro zřizování cloudu. Při vypnutí by se měla u všech konektorů způsobit Úplná synchronizace.



## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je Azure AD Connect synchronizace cloudu?](what-is-cloud-sync.md)

