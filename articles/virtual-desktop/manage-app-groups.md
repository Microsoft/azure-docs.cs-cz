---
title: Správa skupin aplikací pro virtuální desktopový portál Windows – Azure
description: Správa skupin aplikací virtuálních počítačů s Windows pomocí Azure Portal.
author: Heidilohr
ms.topic: tutorial
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ec26f021ffa581b0713973904c97349df83a08ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91930267"
---
# <a name="tutorial-manage-app-groups-with-the-azure-portal"></a>Kurz: Správa skupin aplikací pomocí Azure Portal

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/manage-app-groups-2019.md).

Výchozí skupina aplikací vytvořená pro nový fond hostitelů virtuálních počítačů s Windows taky publikuje celou plochu. Kromě toho můžete vytvořit jednu nebo více skupin aplikací RemoteApp pro fond hostitelů. Podle tohoto kurzu vytvořte skupinu aplikací RemoteApp a publikujte jednotlivé aplikace v nabídce Start.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte skupinu RemoteApp.
> * Udělení přístupu k aplikacím vzdálené aplikace RemoteApp.

## <a name="create-a-remoteapp-group"></a>Vytvoření skupiny vzdálených aplikací RemoteApp

Pokud jste už vytvořili fond hostitelů a hostitele relací pomocí Azure Portal nebo PowerShellu, můžete přidat skupiny aplikací z Azure Portal pomocí následujícího postupu:

1.  Přihlaste se na [Azure Portal](https://portal.azure.com/).
   
    >[!NOTE]
    > Pokud se přihlašujete k portálu US Gov, použijte [https://portal.azure.us/](https://portal.azure.us/) místo toho.

2.  Vyhledejte a vyberte **virtuální počítač s Windows**.

3. Můžete přidat skupinu aplikací přímo nebo ji můžete přidat z existujícího fondu hostitelů. Vyberte některou z níže uvedených možností:

    - V nabídce na levé straně stránky vyberte **skupiny aplikací** a pak vyberte **+ Přidat**.

    - V nabídce na levé straně obrazovky vyberte **fondy hostitelů** , vyberte název fondu hostitelů, v nabídce na levé straně vyberte **skupiny aplikací** a pak vyberte **+ Přidat**. V takovém případě se fond hostitelů už vybere na kartě základy.

4. Na kartě **základy** vyberte **předplatné** a **skupinu prostředků** , pro které chcete vytvořit skupinu aplikací. Můžete také vytvořit novou skupinu prostředků a nemusíte si vybrat některou z existujících.

5. Z rozevírací nabídky vyberte **fond hostitelů** , který bude přidružen ke skupině aplikací.

    >[!NOTE]
    >Musíte vybrat fond hostitelů přidružený ke skupině aplikací. Skupiny aplikací mají aplikace nebo plochy, které jsou obsluhovány od hostitele relace, a hostitelé relace jsou součástí fondů hostitelů. Skupina aplikací musí být během vytváření přidružená ke fondu hostitelů.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky karty základy v Azure Portal.](media/basics-tab.png)

6. V části **typ skupiny aplikací** vyberte **Vzdálená aplikace RemoteApp** a potom zadejte název vaší aplikace RemoteApp.

      > [!div class="mx-imgBorder"]
      > ![Snímek obrazovky s poli typu skupiny aplikací. Je zvýrazněna Vzdálená aplikace RemoteApp.](media/remoteapp-button.png)

7.  Vyberte **Další: přiřazení >** kartě.

8.  Pokud chcete přiřadit jednotlivé uživatele nebo skupiny uživatelů ke skupině aplikací, vyberte **+ Přidat uživatele nebo skupiny uživatelů Azure AD**.

9.  Vyberte uživatele, kteří mají mít přístup k aplikacím. Můžete vybrat jednoho nebo více uživatelů a skupin uživatelů.

     > [!div class="mx-imgBorder"]
     > ![Snímek obrazovky nabídky výběru uživatele](media/select-users.png)

10.  Vyberte **Vybrat**.

11.  Vyberte **Další: aplikace >** a pak vyberte **+ Přidat aplikace**.

12.  Přidání aplikace z nabídky Start:

      - V části **zdroj aplikace** v rozevírací nabídce vyberte **Nabídka Start** . Dále v části **aplikace** vyberte aplikaci z rozevírací nabídky.

     > [!div class="mx-imgBorder"]
     > ![Snímek obrazovky obrazovky Přidat aplikaci s vybranou nabídkou Start](media/add-app-start.png)

      - Do pole **Zobrazovaný název** zadejte název aplikace, který se zobrazí uživateli na svém klientovi.

      - Ostatní možnosti ponechte zapnuté a vyberte **Uložit**.

13.  Přidání aplikace z konkrétní cesty k souboru:

      - V části **zdroj aplikace** vyberte v rozevírací nabídce možnost **cesta k souboru** .

      - Do pole **cesta k aplikaci** zadejte cestu k aplikaci na hostiteli relace registrovaném s přidruženým fondem hostitelů.

      - Zadejte podrobnosti aplikace do polí **název aplikace**, **Zobrazovaný název**, **cesta ikony** a **index ikony** .

      - Vyberte **Uložit**.

     > [!div class="mx-imgBorder"]
     > ![Snímek obrazovky se stránkou přidat aplikaci s vybranou cestou k souboru](media/add-app-file.png)

14.  Tento postup opakujte pro každou aplikaci, kterou chcete přidat do skupiny aplikací.

15.  Potom vyberte **Další: >pracovního prostoru**.

16.  Pokud chcete zaregistrovat skupinu aplikací k pracovnímu prostoru, pro **registraci skupiny aplikací** vyberte **Ano** . Pokud místo toho chcete skupinu aplikací zaregistrovat později, vyberte možnost **ne**.

17.  Pokud vyberete **Ano**, můžete vybrat existující pracovní prostor, do kterého se má skupina aplikací zaregistrovat.

       >[!NOTE]
       >Skupinu aplikací můžete zaregistrovat jenom k pracovním prostorům vytvořeným ve stejném umístění jako fond hostitelů. Lze. Pokud jste předtím zaregistrovali jinou skupinu aplikací ze stejného fondu hostitelů jako novou skupinu aplikací k pracovnímu prostoru, bude vybraná a nebude možné ji upravit. Všechny skupiny aplikací z fondu hostitelů musí být zaregistrované do stejného pracovního prostoru.

     > [!div class="mx-imgBorder"]
     > ![Snímek obrazovky se stránkou zaregistrovat skupinu aplikací pro už existující pracovní prostor Fond hostitelů je předvybraný.](media/register-existing.png)

18.  Případně, pokud chcete vytvořit značky, které usnadňují uspořádání vašeho pracovního prostoru, vyberte **Další: značky >** a zadejte názvy značek.

19.  Až budete hotovi, vyberte **zkontrolovat + vytvořit**.

20.  Počkejte, než se proces ověření dokončí. Až to bude hotové, vyberte **vytvořit** a nasaďte skupinu aplikací.

Proces nasazení provede následující akce:

- Vytvořte skupinu aplikace RemoteApp.
- Přidejte vybrané aplikace do skupiny aplikací.
- Publikujte skupinu aplikací publikovanou pro uživatele a skupiny uživatelů, které jste vybrali.
- Pokud se rozhodnete tuto skupinu aplikací provést, zaregistrujte ji.
- Vytvořte odkaz na šablonu Azure Resource Manager na základě konfigurace, kterou si můžete stáhnout a uložit pro pozdější verzi.

>[!IMPORTANT]
>Pro každého tenanta Azure Active Directory lze vytvořit pouze 200 skupin aplikací. Tento limit jsme přidali kvůli omezením služby pro načítání informačních kanálů pro naše uživatele. Toto omezení se nevztahuje na skupiny aplikací vytvořené ve virtuální ploše Windows (Classic).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit skupinu aplikací, naplnit ji v aplikacích RemoteApp a přiřazovat uživatele do skupiny aplikací. Další informace o tom, jak vytvořit fond ověřovacích hostitelů, najdete v následujícím kurzu. Pomocí fondu hostitelů ověření můžete monitorovat aktualizace služby před jejich vrácením do produkčního prostředí.

> [!div class="nextstepaction"]
> [Vytvoření fondu hostitelů pro ověření aktualizací služeb](./create-validation-host-pool.md)
