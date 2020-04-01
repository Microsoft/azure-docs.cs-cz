---
title: Povolení samoobslužného resetování hesla služby Azure Active Directory
description: V tomto kurzu se dozvíte, jak povolit samoobslužné resetování hesla služby Azure Active Directory pro skupinu uživatelů a otestovat proces resetování hesla.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71b9052f364dfbae205dd324ba69de9578ccc225
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77027675"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Kurz: Umožněte uživatelům odemknout účet nebo resetovat hesla pomocí samoobslužného resetování hesla služby Azure Active Directory.

Azure Active Directory (Azure AD) samoobslužné resetování hesla (SSPR) umožňuje uživatelům změnit nebo resetovat své heslo, bez zapojení správce nebo help desk. Pokud je uživatelský účet uzamčen nebo zapomene heslo, může sledovat výzvy k odblokování a vrácení se do práce. Tato možnost snižuje volání technické podpory a ztrátu produktivity, když se uživatel nemůže přihlásit ke svému zařízení nebo aplikaci.

> [!IMPORTANT]
> Tento rychlý start ukazuje správci, jak povolit samoobslužné resetování hesla. Pokud jste koncový uživatel, který se již zaregistroval pro samoobslužné https://aka.ms/ssprresetování hesla a potřebujete se vrátit ke svému účtu, přejděte na .
>
> Pokud váš IT tým nepovolil možnost resetovat vlastní heslo, obraťte se na helpdesk a opomocněte se.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Povolení samoobslužného resetování hesla pro skupinu uživatelů Azure AD
> * Konfigurace metod ověřování a možností registrace
> * Testování procesu ověřování výsledků uživatelských práv jako uživatele

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Funkční tenant Azure AD, který má přiřazenou alespoň zkušební licenci.
    * V případě potřeby [si jej vytvořte zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Účet s oprávněními *globálního správce.*
* Uživatel bez oprávnění správce s heslem, které znáte, například *testuser*. Testování prostředí Samoobslužné správě klienta koncového uživatele pomocí tohoto účtu v tomto kurzu.
    * Pokud potřebujete vytvořit uživatele, přečtěte [si úvodní příručku: Přidání nových uživatelů do služby Azure Active Directory](../add-users-azure-active-directory.md).
* Skupina, jejíž členem je uživatel, jehož členem není správce, například *skupina SSPR-Test-Group*. V tomto kurzu povolíte pro tuto skupinu sspr.
    * Pokud potřebujete vytvořit skupinu, přečtěte si, jak [vytvořit skupinu a přidat členy ve službě Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Povolení samoobslužného resetování hesel

Azure AD umožňuje povolit sspr pro *žádné*, *vybrané*nebo *všechny* uživatele. Tato podrobná schopnost umožňuje zvolit podmnožinu uživatelů k testování procesu registrace a pracovního postupu s snop. Pokud jste spokojeni s procesem a můžete komunikovat požadavky s širší skupinou uživatelů, můžete vybrat další skupiny uživatelů povolit pro sspr. Nebo můžete povolit sspr pro všechny v tenantovi Azure AD.

V tomto kurzu nakonfigurujte místní snop pro sadu uživatelů v testovací skupině. V následujícím příkladu se používá skupina *SSPR-Test-Group.* Podle potřeby poskytněte vlastní skupinu Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu s oprávněními *globálního správce.*
1. Vyhledejte a vyberte **Službu Azure Active Directory**a v nabídce na levé straně zvolte **Resetování hesla.**
1. Na stránce Vlastnosti zvolte v části **Vlastnosti** *možnost Samoobslužné resetování hesla*možnost Vybrat **skupinu.**
1. Vyhledejte a vyberte skupinu Azure AD, například *Skupinu s protokolem SSPR-Test-Group*, a pak zvolte *Vybrat*.

    [![](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png "Select a group in the Azure portal to enable for self-service password reset")](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

    Jako součást širšínasazení sspr, vnořené skupiny jsou podporovány. Ujistěte se, že uživatelé ve skupinách, které zvolíte, mají přiřazeny příslušné licence. V současné době neexistuje žádný proces ověření těchto licenčních požadavků.

1. Chcete-li povolit automatické uživatelské účty pro vybrané uživatele, vyberte **možnost Uložit**.

## <a name="select-authentication-methods-and-registration-options"></a>Výběr metod ověřování a možností registrace

Když uživatelé potřebují odemknout svůj účet nebo resetovat heslo, zobrazí se výzva k zadání další metody potvrzení. Tento dodatečný faktor ověřování zajišťuje, že jsou dokončeny pouze schválené události spr pr. Na základě registračních informací, které uživatel poskytne, můžete zvolit, které metody ověřování povolíte.

1. Na stránce **Metody ověřování** z nabídky na levé straně nastavte počet **metod potřebných k obnovení** na *hodnotu 1*.

    Chcete-li zlepšit zabezpečení, můžete zvýšit počet metod ověřování požadovaných pro ověřování pravosti.

1. Zvolte **metody dostupné uživatelům,** které vaše organizace chce povolit. V tomto kurzu zaškrtněte políčka, chcete-li povolit následující metody:

    * *Oznámení mobilní aplikace*
    * *Kód mobilní aplikace*
    * *E-mail*
    * *Mobilní telefon*
    * *Telefon do kanceláře*

1. Chcete-li použít metody ověřování, vyberte **uložit**.

Než uživatelé mohou odemknout svůj účet nebo resetovat heslo, musí zaregistrovat své kontaktní údaje. Tyto kontaktní informace se používají pro různé metody ověřování nakonfigurované v předchozích krocích.

Správce může tyto kontaktní informace poskytnout ručně nebo uživatelé mohou přejít na registrační portál a poskytnout je sami. V tomto kurzu nakonfigurujte uživatele, aby byli při příštím přihlášení vyzváni k registraci.

1. Na stránce **Registrace** v nabídce na levé straně vyberte *Ano,* **aby se uživatelé při přihlašování registrovali.**
1. Je důležité, aby kontaktní informace byly průběžně aktuální. Pokud jsou kontaktní informace při spuštění události sspr zastaralé, uživatel nemusí být schopen odemknout svůj účet nebo obnovit své heslo.

    Nastavte **Počet dní před vyzváním uživatelů k potvrzení ověřovacích informací** na *180*.
1. Chcete-li použít nastavení registrace, vyberte **uložit**.

## <a name="configure-notifications-and-customizations"></a>Konfigurace oznámení a vlastního nastavení

Chcete-li uživatele informovat o aktivitě účtu, můžete nakonfigurovat e-mailová oznámení, která mají být odeslána, když dojde k události programu SSPR. Tato oznámení se mohou vztahovat jak na běžné uživatelské účty, tak na účty správců. Pro účty správce toto oznámení poskytuje další vrstvu povědomí při resetování hesla účtu privilegovaného správce pomocí služby SSPR.

1. Na stránce **Oznámení** z nabídky na levé straně nakonfigurujte následující možnosti:

   * Možnost **Upozornit uživatele na resetování hesla** nastavte na *Ano*.
   * Možnost **Upozornit všechny správce na resetování hesla jiného správce** nastavte na *Ano*.

1. Chcete-li použít předvolby oznámení, vyberte **Uložit**.

Pokud uživatelé potřebují další pomoc s procesem sspr, můžete přizpůsobit odkaz na "Kontaktujte správce". Tento odkaz se používá v procesu registrace sspr a když uživatel odemkne svůj účet nebo resetuje své heslo. Chcete-li zajistit, aby uživatelé získali potřebnou podporu, důrazně doporučujeme poskytnout vlastní e-mail technické podpory nebo adresu URL.

1. Na stránce **Přizpůsobení** v nabídce na levé straně nastavte *odkaz Přizpůsobit helpdesk* na **Ano**.
1. V poli **Vlastní helpdesk e-mail nebo adresa URL** zadejte e-mailovou adresu nebo adresu URL webové stránky, kde mohou uživatelé získat další nápovědu od vaší organizace, například*https://support.contoso.com/*
1. Chcete-li použít vlastní odkaz, vyberte **Uložit**.

## <a name="test-self-service-password-reset"></a>Testování samoobslužného resetování hesla

Pokud je povoleno a nakonfigurováno samodotykové ověřování dat, otestujte proces samopalu s uživatelem, který je součástí skupiny, kterou jste vybrali v předchozí části, například *Test-SSPR-Group*. V následujícím příkladu se používá *účet testuser.* Zadejte svůj vlastní uživatelský účet, který je součástí skupiny, kterou jste povolili pro samoobslužné ověřování dat v první části tohoto kurzu.

> [!NOTE]
> Při testování samoobslužného resetování hesla použijte účet bez oprávnění správce. Správci jsou vždy povoleny pro samoobslužné resetování hesla a jsou povinni používat dvě metody ověřování k resetování hesla.

1. Postup ruční registrace zobrazíte v novém okně prohlížeče v režimu InPrivate [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)nebo inkognito a přejděte na . Uživatelé by měli být přesměrováni na tento registrační portál při příštím přihlášení.
1. Přihlaste se pomocí testovacího uživatele, který není správcem, například *testuser*, a zaregistrujte kontaktní informace o metodách ověřování.
1. Po dokončení vyberte tlačítko označené **Vypadá dobře** a zavřete okno prohlížeče.
1. Otevřete nové okno prohlížeče v režimu InPrivate nebo [https://aka.ms/sspr](https://aka.ms/sspr)inkognito a přejděte na .
1. Zadejte informace o účtu uživatelů, kteří nejsou oprávněním správce, například *testuser*, znaky z CAPTCHA a pak vyberte **Další**.

    ![Zadáním informací o uživatelském účtu pro resetování hesla](media/tutorial-enable-sspr/password-reset-page.png)

1. Chcete-li obnovit heslo, postupujte podle ověřovacích pokynů. Po dokončení byste měli obdržet e-mailové oznámení, že heslo bylo resetováno.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V následujícím kurzu v této sérii nakonfigurujete zpětný zápis hesla. Tato funkce zapisuje změny hesla z Azure AD SSPR zpět do místního prostředí služby AD. Pokud chcete pokračovat v této sérii kurzů ke konfiguraci zpětného zápisu hesla, nezakažte nyní sspr.

Pokud již nechcete používat funkci sspr, kterou jste nakonfigurovali jako součást tohoto kurzu, nastavte stav sspr na **žádný** pomocí následujících kroků:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyhledejte a vyberte **Službu Azure Active Directory**a v nabídce na levé straně zvolte **Resetování hesla.**
1. Na stránce **Vlastnosti** vyberte v části *Možnost Samoobslužné resetování hesla* **možnost Žádný**.
1. Chcete-li použít změnu snop, vyberte **uložit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili samoobslužné resetování hesla Azure AD pro vybranou skupinu uživatelů. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Povolení samoobslužného resetování hesla pro skupinu uživatelů Azure AD
> * Konfigurace metod ověřování a možností registrace
> * Testování procesu ověřování výsledků uživatelských práv jako uživatele

> [!div class="nextstepaction"]
> [Povolení služby Azure Multi-Factor Authentication](tutorial-mfa-applications.md)
