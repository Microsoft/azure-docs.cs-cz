---
title: Vytváření a správa uživatelů
description: Vytvářejte a spravujte uživatele senzorů a místní konzoly pro správu. Uživatelům je možné přiřadit roli správce, analytika zabezpečení nebo uživatel s oprávněním jen pro čtení.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/03/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: dff379c99fa7383c7f7844cf8d195a345e88a335
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466265"
---
# <a name="about-defender-for-iot-console-users"></a>O programu Defender pro uživatele konzoly IoT

Tento článek popisuje, jak vytvořit a spravovat uživatele senzorů a místní konzoly pro správu. Role uživatelů zahrnují správce, analytika zabezpečení nebo uživatele jen pro čtení. Každá role je přidružená k rozsahu oprávnění k nástrojům pro senzor nebo místní konzolu pro správu. Role jsou navržené tak, aby usnadnily podrobný a zabezpečený přístup k Azure Defenderu pro IoT.

K dispozici jsou také funkce ke sledování aktivity uživatelů a povolení přihlášení ke službě Active Directory.

Ve výchozím nastavení se každý senzor a místní Konzola pro správu instalují s *CyberX a uživatel podpory* . Tito uživatelé mají přístup k pokročilým nástrojům pro řešení potíží a instalaci. Uživatelé s právy pro správce by se měli přihlásit pomocí těchto přihlašovacích údajů uživatele, vytvořit správce a pak vytvořit další uživatele pro analytiky zabezpečení a uživatele jen pro čtení.

## <a name="role-based-permissions"></a>Oprávnění na základě rolí
K dispozici jsou následující role uživatelů:

- Jen **pro čtení**: uživatelé s oprávněními jen pro čtení provádějí úlohy, jako je zobrazení výstrah a zařízení na mapě zařízení. Tito uživatelé mají přístup k možnostem zobrazeným v části **Navigace**.

- **Analytik zabezpečení**: analytici zabezpečení mají oprávnění uživatele jen pro čtení. Můžou také provádět akce na zařízeních, potvrdit výstrahy a používat nástroje pro vyšetřování. Tito uživatelé mají přístup k možnostem zobrazeným v části **Navigace** a **Analýza**.

- **Správce**: Správci mají přístup ke všem nástrojům, včetně definování systémových konfigurací, vytváření a správě uživatelů a dalších. Tito uživatelé mají přístup k možnostem zobrazeným v oblasti **Navigace**, **Analýza** a **Správa**.

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>Oprávnění na základě rolí u místních nástrojů konzoly pro správu

Tato část popisuje oprávnění dostupná pro správce, analytiky zabezpečení a uživatele jen pro čtení pro místní konzolu pro správu.  

| Oprávnění | Jen pro čtení | Analytik zabezpečení | Správce |
|--|--|--|--|
| Zobrazení a filtrování podnikové mapy | ✓ | ✓ | ✓ |
| Sestavení webu |  |  | ✓ |
| Správa lokality (přidávání a úpravy zón) |  |  | ✓ |
| Zobrazení a filtrování inventáře zařízení | ✓ | ✓ | ✓ |
| Zobrazení a správa výstrah: potvrzení, učení a připnutí | ✓ | ✓ | ✓ |
| Generování sestav |  | ✓ | ✓ |
| Zobrazit sestavy posouzení rizik |  | ✓ | ✓ |
| Nastavit vyloučení výstrah |  | ✓ | ✓ |
| Zobrazení nebo definování skupin přístupu |  |  | ✓ |
| Spravovat nastavení systému |  |  | ✓ |
| Správa uživatelů |  |  | ✓ |
| Odesílat data výstrah partnerům |  |  | ✓ |
| Správa certifikátů |  |  | ✓ |
| Časový limit relace, když uživatelé nejsou aktivní | 30 minut | 30 minut  | 30 minut  |

#### <a name="assign-users-to-access-groups"></a>Přiřazení uživatelů k skupinám přístupu

Správci můžou rozšířit řízení přístupu uživatele v programu Defender pro IoT tím, že přiřadí uživatele ke konkrétním *skupinám přístupu*. Přístupové skupiny se přiřazují zónám, lokalitám, oblastem a obchodním jednotkám, kde se nachází senzor. Díky přiřazování uživatelů k přístupovým skupinám získá správce konkrétní kontrolu nad tím, kde uživatelé spravují a analyzují zjišťování zařízení. 

Pracovní postup bude vyhovovat velkým organizacím, kde uživatelská oprávnění můžou být složitá nebo určená globálními zásadami zabezpečení organizace. Další informace najdete v tématu [definování globálního řízení přístupu](how-to-define-global-user-access-control.md).

### <a name="role-based-permissions-to-sensor-tools"></a>Oprávnění založená na rolích k nástrojům senzorů

Tato část popisuje oprávnění, která jsou k dispozici správcům senzorů, analytikům zabezpečení a uživatelům s oprávněním jen pro čtení.  

| Oprávnění | Jen pro čtení | Analytik zabezpečení | Správce |
|--|--|--|--|
| Zobrazení řídicího panelu | ✓ | ✓ | ✓ |
| Zobrazení přiblížení mapy ovládacích prvků |  |  | ✓ |
| Zobrazení upozornění | ✓ | ✓ | ✓ |
| Správa výstrah: potvrzení, učení a připnutí |  | ✓ | ✓ |
| Zobrazení událostí na časové ose |  | ✓ | ✓ |
| Autorizovat zařízení, známá skenovací zařízení, programovací zařízení |  | ✓ | ✓ |
| Zobrazit data pro šetření | ✓ | ✓ | ✓ |
| Spravovat nastavení systému |  |  | ✓ |
| Správa uživatelů |  |  | ✓ |
| Servery DNS pro zpětné vyhledávání |  |  | ✓ |
| Odesílat data výstrah partnerům |  | ✓ | ✓ |
| Vytváření komentářů k výstrahám |  | ✓ | ✓ |
| Zobrazení historie změn programování | ✓ | ✓ | ✓ |
| Vytváření přizpůsobených pravidel upozornění |  | ✓ | ✓ |
| Správa více oznámení současně |  | ✓ | ✓ |
| Správa certifikátů |  |  | ✓ |
| Časový limit relace, když uživatelé nejsou aktivní | 30 minut | 30 minut | 30 minut |

## <a name="define-users"></a>Definování uživatelů

Tato část popisuje, jak definovat uživatele. CyberX, podpora a správci můžou přidávat, odebírat a aktualizovat další definice uživatelů.

Definování uživatele:

1. V levém podokně senzoru nebo místní konzoly pro správu vyberte **Uživatelé**.
1. V okně **Uživatelé** vyberte **vytvořit uživatele**.
1. V podokně **vytvořit uživatele** definujte následující parametry:

   - **Uživatelské jméno**: zadejte uživatelské jméno.
   - **E-mail**: Zadejte e-mailovou adresu uživatele.
   - **Jméno: zadejte** jméno uživatele.
   - **Příjmení**: zadejte poslední jméno uživatele.
   - **Role**: Definujte roli uživatele. Viz [oprávnění na základě rolí](#role-based-permissions).
   - **Přístup ke skupině**: Pokud vytváříte uživatele pro místní konzolu pro správu, definujte přístupovou skupinu uživatele. Viz téma [definování globálního řízení přístupu](how-to-define-global-user-access-control.md).
   - **Heslo**: Vyberte typ uživatele následujícím způsobem:
     - **Místní uživatel**: definujte heslo pro uživatele snímače nebo místní konzoly pro správu. Heslo musí obsahovat alespoň šest znaků a musí obsahovat písmena a číslice.
     - **Uživatel služby Active Directory**: můžete uživatelům dovolit, aby se přihlásili ke senzoru nebo konzole pro správu pomocí pověření služby Active Directory. Definované skupiny služby Active Directory je možné přidružit ke konkrétním úrovním oprávnění. Můžete například nakonfigurovat konkrétní skupinu služby Active Directory a přiřadit všechny uživatele ve skupině k typu uživatele jen pro čtení.

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="Spravujte své uživatele.":::

## <a name="user-session-timeout"></a>Časový limit uživatelské relace

Pokud uživatelé nejsou na klávesnici nebo myši po určitou dobu aktivní, odhlásí se z relace a musí se znovu přihlásit.

Pokud uživatelé nepracovali s myší konzoly nebo klávesnicí po dobu 30 minut, odhlášení relace je vynuceno.

Tato funkce je ve výchozím nastavení povolená a při upgradu může být zakázaná. Kromě toho je možné aktualizovat časy počítání relací. Časy relací se definují v sekundách. Definice se aplikují na senzor a místní konzolu pro správu.

Po uplynutí časového limitu neaktivity se v konzole zobrazí zpráva s časovým limitem relace.

### <a name="control-inactivity-sign-out"></a>Řízení nečinnosti při odhlašování

Uživatelé s oprávněním správce můžou povolit nebo zakázat odhlášení nečinnosti a upravit prahové hodnoty nečinnosti.

Přístup k příkazu:

1. Přihlaste se k rozhraní příkazového řádku pro senzor nebo místní konzolu pro správu pomocí programu Defender pro přihlašovací údaje správce IoT.

1. Zadejte `sudo nano /var/cyberx/properties/authentication`.

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

Chcete-li funkci zakázat, změňte `infinity_session_expiration = true` na `infinity_session_expiration = false` .

Chcete-li aktualizovat účetní období odhlášení, upravte `= <number>` hodnotu na požadovaný čas.

## <a name="track-user-activity"></a>Sledovat aktivitu uživatele 

Aktivitu uživatele můžete sledovat v časové ose události u každého snímače. Časová osa zobrazuje událost nebo ovlivněné zařízení a čas a datum, kdy uživatel aktivitu provedl.

Postup zobrazení aktivity uživatele:

1. Přihlaste se ke senzoru.
1. V časové ose události povolte možnost **operace uživatele** . 

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="Zobrazit aktivitu uživatele.":::

## <a name="integrate-with-active-directory-servers"></a>Integrace se servery služby Active Directory 

Nakonfigurujte senzor nebo místní konzolu pro správu tak, aby fungovaly se službou Active Directory. Díky tomu mohou uživatelé služby Active Directory přistupovat k programu Defender pro konzoly IoT pomocí svých přihlašovacích údajů služby Active Directory.

Podporují se dva typy ověřování pomocí protokolu LDAP:

- **Úplné ověřování**: informace o uživateli se načítají ze serveru LDAP. Příkladem může být křestní jméno, příjmení, e-mail a uživatelská oprávnění.

- **Důvěryhodný uživatel**: bylo načteno pouze heslo uživatele. Další načtené podrobnosti o uživatelích vycházejí z uživatelů definovaných ve snímači.

### <a name="active-directory-and-defender-for-iot-permissions"></a>Active Directory a Defender pro oprávnění IoT

Můžete přidružit skupiny služby Active Directory, které jsou tady definované, s určitými úrovněmi oprávnění. Můžete například nakonfigurovat konkrétní skupinu služby Active Directory a přiřadit oprávnění jen pro čtení všem uživatelům ve skupině.

Konfigurace služby Active Directory:

1. V levém podokně vyberte **nastavení systému**.

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="Zobrazení nastavení systému služby Active Directory.":::

2. V podokně **nastavení systému** vyberte **Active Directory**.

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="Upravte konfigurace služby Active Directory.":::

3. V dialogovém okně **Upravit konfiguraci služby Active Directory** vyberte možnost **Povolit integraci služby Active Directory**–  >  **Uložit**. Dialogové okno **Upravit konfiguraci služby Active Directory** se rozbalí a teď můžete zadat parametry pro konfiguraci služby Active Directory.

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="Zadejte parametry pro konfiguraci služby Active Directory.":::

    > [!NOTE]
    > - Parametry LDAP je třeba definovat přesně tak, jak se zobrazí ve službě Active Directory.
    > - Pro všechny parametry služby Active Directory používejte pouze malá a velká písmena. Používejte malá písmena, i když konfigurace ve službě Active Directory používají velká písmena.
    > - Pro stejnou doménu nemůžete nakonfigurovat protokoly LDAP i LDAP. Můžete však použít obojí pro různé domény současně.

4. Nastavte parametry serveru služby Active Directory následujícím způsobem:

   | Parametr serveru | Popis |
   |--|--|
   | Plně kvalifikovaný název domény řadiče domény | Nastavte plně kvalifikovaný název domény (FQDN) přesně tak, jak se zobrazuje na vašem serveru LDAP. Zadejte například `host1.subdomain.domain.com`. |
   | Port řadiče domény | Zadejte port, na kterém je nakonfigurovaný protokol LDAP. |
   | Primární doména | V závislosti na konfiguraci LDAP nastavte název domény (například `subdomain.domain.com` ) a typ připojení. |
   | Skupiny služby Active Directory | Zadejte názvy skupin, které jsou definované v konfiguraci služby Active Directory na serveru LDAP. |
   | Důvěryhodné domény | Chcete-li přidat důvěryhodnou doménu, přidejte název domény a typ připojení důvěryhodné domény. <br />Důvěryhodné domény můžete nakonfigurovat jenom pro uživatele, kteří byli definováni v části Uživatelé. |

#### <a name="activedirectory-groups-for-the-on-premises-management-console"></a>Skupiny služby Active Directory pro místní konzolu pro správu

Pokud vytváříte skupiny služby Active Directory pro místní uživatele konzoly pro správu, je nutné vytvořit pravidlo skupiny přístupu pro každou skupinu služby Active Directory. Místní Konzola pro správu: přihlašovací údaje služby Active Directory nebudou fungovat, pokud pro skupinu uživatelů Active Directory neexistuje pravidlo pro přístup skupiny uživatelů. Viz téma [definování globálního řízení přístupu](how-to-define-global-user-access-control.md).

1. Vyberte **Uložit**.

2. Chcete-li přidat důvěryhodný server, vyberte možnost **Přidat server** a nakonfigurujte jiný server.

## <a name="resetting-passwords"></a>Resetování hesel

### <a name="cyberx-or-support-user"></a>CyberX nebo podpora uživatele

K funkci **obnovení hesla** mají přístup jenom **CyberX** a uživatelé **podpory** . Pokud uživatel **CyberX** nebo **Podpora** zapomněl heslo, můžete heslo resetovat prostřednictvím možnosti **obnovení hesla** na přihlašovací stránce Defender for IoT.

Resetování hesla pro uživatele CyberX nebo podpory:

1. Na přihlašovací obrazovce Defender for IoT vyberte  **obnovení hesla**. Otevře se obrazovka **pro obnovení hesla** .

1. Vyberte buď **CyberX** , nebo **podporu**, a zkopírujte jedinečný identifikátor.

1. Přejděte na Azure Portal a vyberte **weby a senzory**.  

1. Na horním panelu nástrojů vyberte ikonu **filtru předplatného** :::image type="icon" source="media/password-recovery-images/subscription-icon.png" border="false":::  a vyberte předplatné, ke kterému je senzor připojený.

1. Vyberte kartu **obnovit místní heslo konzoly pro správu** .

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="Kliknutím na tlačítko obnovit místní správu Stáhněte soubor pro obnovení.":::

1. Zadejte jedinečný identifikátor, který jste dostali na obrazovce **pro obnovení hesla** , a vyberte **obnovit**. `password_recovery.zip`Soubor se stáhne.

    > [!NOTE]
    > Neměňte soubor pro obnovení hesla. Jedná se o podepsaný soubor a nebude fungovat, pokud s ním budete manipulovat.

1. Na obrazovce **obnovení hesla** vyberte **Odeslat**. Otevře se okno **Odeslat soubor pro obnovení hesla** .

1. Vyberte **Procházet** a vyhledejte `password_recovery.zip` soubor, nebo ho přetáhněte `password_recovery.zip` do okna.

    > [!NOTE]
    > Může se zobrazit chybová zpráva oznamující, že soubor není platný. Chcete-li opravit tuto chybovou zprávu, ujistěte se, že jste před stažením `password_recovery.zip` a stažení znovu vybrali správné předplatné a znovu si ho stáhněte.  

1. Pak se zobrazí možnost **Další** a uživatel a heslo generované systémem pro konzolu pro správu.

### <a name="administrator-security-analyst-and-read-only-user"></a>Správce, analytik zabezpečení a uživatel jen pro čtení

Jenom pro čtení a analytici zabezpečení nemůžou resetovat svoje heslo a musí se obrátit na uživatele s rolemi správce, podpora nebo CyberX, aby se resetoval jeho heslo. Uživatel s právy pro správu musí požádat o resetování hesla v **CyberX** nebo **podpoře** uživatele.

Resetování hesla uživatele na senzoru:

1. Uživatel role správce, podpory nebo CyberX by se měl přihlásit ke senzoru.

1. Na levém panelu vyberte **Uživatelé** .

   :::image type="content" source="media/password-recovery-images/sensor-page.png" alt-text="V levém podokně vyberte možnost uživatel.":::

1. Vyhledejte uživatele a v rozevírací nabídce **Akce** vyberte možnost **Upravit** .

   :::image type="content" source="media/password-recovery-images/edit.png" alt-text="v rozevírací nabídce Akce vyberte Upravit.":::

1. Do pole **nové heslo** zadejte nové heslo a **potvrďte nové** heslo.

1. Vyberte **Aktualizovat**.

Resetování hesla uživatele v místní konzole pro správu:

1. Uživatel role správce, podpory nebo CyberX by se měl přihlásit ke senzoru.

1. Na levém panelu vyberte **Uživatelé** .

   :::image type="content" source="media/password-recovery-images/console-page.png" alt-text="Na levém panelu vyberte možnost uživatele.":::

1. Vyhledejte uživatele a vyberte ikonu upravit :::image type="icon" source="media/password-recovery-images/edit-icon.png" border="false"::: .

1. Do pole **nové heslo** zadejte nové heslo a **potvrďte nové** heslo.

1. Vyberte **Aktualizovat**.

## <a name="see-also"></a>Viz také

[Aktivace a nastavení senzoru](how-to-activate-and-set-up-your-sensor.md) 
 [Aktivace a nastavení místní konzoly](how-to-activate-and-set-up-your-on-premises-management-console.md) 
 pro správu [Sledovat aktivitu snímače](how-to-track-sensor-activity.md)
