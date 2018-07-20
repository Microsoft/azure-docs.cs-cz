---
title: Postup konfigurace zpětného zápisu hesla pro samoobslužné resetování HESLA Azure AD
description: Pomocí služby Azure AD a zpětný zápis hesla do místního adresáře služby Azure AD Connect
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: e613ff742096077fe1765d4b855b6c7d409cc228
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158943"
---
# <a name="how-to-configure-password-writeback"></a>Postupy: Konfigurace zpětného zápisu hesla

Doporučujeme používat funkci Automatické aktualizace [Azure AD Connect](./../connect/active-directory-aadconnect-get-started-express.md) při použití zpětný zápis hesla.

Následující postup předpokládá, že jste už nakonfigurovali Azure AD Connect ve vašem prostředí s použitím [Express](./../connect/active-directory-aadconnect-get-started-express.md) nebo [vlastní](./../connect/active-directory-aadconnect-get-started-custom.md) nastavení.

1. Ke konfiguraci a povolení zpětného zápisu hesla, přihlaste se k serveru Azure AD Connect a spustit **Azure AD Connect** Průvodce konfigurací.
2. Na **úvodní** stránce **konfigurovat**.
3. Na **další úkoly** stránce **přizpůsobit možnosti synchronizace**a pak vyberte **Další**.
4. Na **připojit ke službě Azure AD** stránky, zadejte přihlašovací údaje globálního správce a pak vyberte **Další**.
5. Na **připojení adresáře** a **domén/organizačních jednotek** filtrování stránky, vyberte **Další**.
6. Na **volitelné funkce** stránce, zaškrtněte políčko vedle položky **zpětný zápis hesla** a vyberte **Další**.
   ![Povolení zpětného zápisu hesla ve službě Azure AD Connect][Writeback]
7. Na **připraveno ke konfiguraci** stránce **konfigurovat** a počkejte na dokončení procesu.
8. Až se dokončit konfiguraci, vyberte **ukončovací**.

Související se zpětným zápisem hesla, běžné úloh odstraňování potíží naleznete v části [řešení potíží se zpětným zápisem hesla](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) v našem článku Poradce při potížích.

## <a name="active-directory-permissions"></a>Oprávnění služby Active Directory

Účet zadané v nástroji Azure AD Connect musí mít následující položky. Pokud chcete být v rozsahu pro samoobslužné resetování HESLA:

* **Resetování hesla** 
* **Změnit heslo** 
* **Oprávnění k zápisu** na `lockoutTime`
* **Oprávnění k zápisu** na `pwdLastSet`
* **Rozšířená oprávnění** buď:
   * Kořenový objekt *každou doménu* v této doménové struktuře
   * Uživatelské organizační jednotky (OU), kterou chcete být v rozsahu pro samoobslužné resetování HESLA

Pokud si nejste jisti které účtu popsané účtu odkazuje na otevřete konfigurace Azure Active Directory Connect uživatelského rozhraní a klikněte **zobrazit aktuální konfiguraci** možnost. Účet, který je potřeba přidat oprávnění je uvedený v části **synchronizaci adresářů**.

Pokud tato oprávnění můžete nastavit, lze účtu služby MA pro každou doménovou strukturu spravovat hesla v zastoupení uživatelských účtů v dané doménové struktuře. 

> [!IMPORTANT]
> Pokud opomenete toto přiřazení oprávnění, pak, i když je potřeba nakonfigurovat správně, zobrazí se zpětný zápis uživatelů dojde k chybě při pokusu o ke správě svých místních hesel z cloudu.
>

> [!NOTE]
> To může trvat až hodinu nebo déle těchto oprávnění k replikaci adresáře ke všem objektům ve vašem adresáři.
>

Pokud chcete nastavit příslušná oprávnění pro zpětný zápis hesla na výskyt, proveďte následující kroky:

1. Otevřete pomocí účtu, který má oprávnění pro správu příslušné domény Active Directory Users and Computers.
2. Z **zobrazení** nabídky, ujistěte se, že **pokročilé funkce** zapnutý.
3. V levém panelu klikněte pravým tlačítkem na objekt, který reprezentuje kořen domény, a vyberte **vlastnosti** > **zabezpečení** > **Upřesnit**.
4. Z **oprávnění** kartu, vyberte možnost **přidat**.
5. Vyberte účet, který oprávnění se používají pro (z instalace služby Azure AD Connect).
6. V **se vztahuje na** rozevíracího seznamu vyberte **podřízené uživatelské** objekty.
7. V části **oprávnění**, zaškrtněte políčka pro následující:
    * **Resetování hesla**
    * **Změnit heslo**
    * **Zápis lockoutTime**
    * **Zápis pwdLastSet**
8. Vyberte **použít/OK** použít změny a zavřete všechna otevřená dialogová.

## <a name="next-steps"></a>Další postup

[Co je zpětný zápis hesla?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Povolení zpětného zápisu hesla ve službě Azure AD Connect"
