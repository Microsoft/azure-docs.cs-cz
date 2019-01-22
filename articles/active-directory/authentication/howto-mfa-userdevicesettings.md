---
title: Správci spravovat uživatele a zařízení – Azure MFA | Dokumentace Microsoftu
description: Popisuje postup změny nastavení uživatele, jako je například vynucení uživatelům znovu provést proces výš.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: c5d2b492d2126aa98012fbbac4008c78ced47e93
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54421500"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Správa nastavení uživatelů pomocí ověřování Azure Multi-Factor Authentication v cloudu

Jako správce můžete spravovat následující nastavení uživatelů a zařízení:

* Vyžadovat, aby uživatelé znovu zadali způsoby kontaktování
* Odstranění hesla aplikace
* Vyžadovat vícefaktorové ověřování na všechna důvěryhodná zařízení 

## <a name="require-users-to-provide-contact-methods-again"></a>Vyžadovat, aby uživatelé znovu zadali způsoby kontaktování
Toto nastavení přinutí uživatel dokončí proces registrace znovu. Neprohlížečové aplikace i nadále fungovat, pokud má uživatel pro jejich hesla aplikací.  Hesla aplikací uživatelům můžete odstranit také výběrem **odstranit všechna stávající hesla aplikací vygenerovaná vybranými uživateli**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Jak vyžadovat, aby uživatelé znovu zadali způsoby kontaktování
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **uživatelé** > **všichni uživatelé**.
3. Na pravé straně vyberte **ověřování službou Multi-Factor Authentication** na panelu nástrojů. Otevře se stránka služby Multi-Factor authentication. 
4. Zaškrtněte políčko vedle uživatelů, které chcete spravovat. Na pravé straně se zobrazí seznam možností rychlý krok. 
5. Vyberte **spravovat uživatelská nastavení**.
6. Zaškrtněte políčko u **vyžadují Vybraní uživatelé znovu zadali způsoby kontaktování**.
   ![Zadali způsoby kontaktování](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Klikněte na **Uložit**.
8. Klikněte na tlačítko **zavřete**.

## <a name="delete-users-existing-app-passwords"></a>Odstranit uživatele existující hesla aplikací
Tato nastavení odstraní všechna hesla aplikací, které uživatel vytvořil. Neprohlížečové aplikace, které byly přidruženy k těmto hesla aplikací přestat fungovat, dokud se nevytvoří nové heslo aplikace.

### <a name="how-to-delete-users-existing-app-passwords"></a>Jak odstranit uživatele existující hesla aplikací
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **uživatelé** > **všichni uživatelé**.
3. Na pravé straně vyberte **ověřování službou Multi-Factor Authentication** na panelu nástrojů. Otevře se stránka služby Multi-Factor authentication. 
6. Zaškrtněte políčko vedle uživatelů, které chcete spravovat. Na pravé straně se zobrazí seznam možností rychlý krok. 
7. Vyberte **spravovat uživatelská nastavení**.
8. Zaškrtněte políčko u **odstranit všechna stávající hesla aplikací vygenerovaná vybranými uživateli**.
   ![Odstranění hesla aplikace](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
9. Klikněte na **Uložit**.
10. Klikněte na tlačítko **zavřete**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Obnovit vícefaktorové ověřování u všech zapamatovaných zařízení pro uživatele
Mezi konfigurovatelné funkce ověřování Azure Multi-Factor Authentication je udělení uživatelům možnost označit zařízení jako důvěryhodné. Další informace najdete v tématu [nastavení konfigurace Azure Multi-Factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

Uživatelé se můžou rozhodnout mimo dvoustupňové ověřování pro Konfigurovatelný počet dnů v jejich běžná zařízení. Pokud dojde k narušení účtu nebo dojde ke ztrátě důvěryhodné zařízení, musíte být schopni odebrat stavu důvěryhodnosti a vyžadovat dvoustupňové ověřování znovu.

**Ověřování službou Multi-Factor Authentication obnovení na všech zapamatovaných zařízeních** nastavení znamená, že bude uživatel odmítl dvoustupňové ověřování při příštím přihlášení, bez ohledu na to, zda se rozhodli označit zařízení jako důvěryhodné. 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Jak obnovit vícefaktorové ověřování na všechny pozastavené zařízení pro uživatele
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **uživatelé** > **všichni uživatelé**.
3. Na pravé straně vyberte **ověřování službou Multi-Factor Authentication** na panelu nástrojů. Otevře se stránka služby Multi-Factor authentication. 
6. Zaškrtněte políčko vedle uživatelů, které chcete spravovat. Na pravé straně se zobrazí seznam možností rychlý krok. 
7. Vyberte **spravovat uživatelská nastavení**.
8. Zaškrtněte políčko u **ověřování službou Multi-Factor Authentication obnovení na všech zapamatovaných zařízeních**
   ![odstranění hesla aplikace](./media/howto-mfa-userdevicesettings/rememberdevices.png)
9. Klikněte na **Uložit**.
10. Klikněte na tlačítko **zavřete**.

## <a name="next-steps"></a>Další postup

- Získejte další informace o tom, jak [nastavení konfigurace Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)

- Pokud uživatelé potřebují pomoc, je směrem k směroval [uživatelskou příručku pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user.md)
