---
title: Správa skupin aplikací pro virtuální desktopový portál Windows – Azure
description: Správa skupin aplikací virtuálních počítačů s Windows pomocí Azure Portal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f072ed8a758173645c886cabf0b20f9e123cbbab
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612118"
---
# <a name="tutorial-manage-app-groups-with-the-azure-portal"></a>Kurz: Správa skupin aplikací pomocí Azure Portal

>[!IMPORTANT]
>Tento obsah se vztahuje na jarní 2020 aktualizaci s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows na verzi 2019 bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/manage-app-groups-2019.md).
>
> V současnosti je ve verzi Public Preview na jaře 2020 aktualizace virtuálních počítačů s Windows. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. 
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Výchozí skupina aplikací vytvořená pro nový fond hostitelů virtuálních počítačů s Windows taky publikuje celou plochu. Kromě toho můžete vytvořit jednu nebo více skupin aplikací RemoteApp pro fond hostitelů. Podle tohoto kurzu vytvořte skupinu aplikací RemoteApp a publikujte jednotlivé aplikace v nabídce Start.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte skupinu RemoteApp.
> * Udělení přístupu k aplikacím vzdálené aplikace RemoteApp.

## <a name="create-a-remoteapp-group"></a>Vytvoření skupiny vzdálených aplikací RemoteApp

Pokud jste už vytvořili fond hostitelů a hostitele relací pomocí Azure Portal nebo PowerShellu, můžete přidat skupiny aplikací z Azure Portal pomocí následujícího postupu:

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2.  Vyhledejte a vyberte **virtuální počítač s Windows**.

3.  V nabídce na levé straně stránky vyberte **skupiny aplikací** a pak vyberte **+ Přidat**.

4. Na kartě **základy** vyberte skupinu předplatných a skupinu prostředků, pro kterou chcete vytvořit skupinu aplikací. Můžete také vytvořit novou skupinu prostředků a nemusíte si vybrat některou z existujících.

5. Z rozevírací nabídky vedle **fondu hostitelů**vyberte fond hostitelů, který bude přidružený ke skupině aplikací.

    >[!NOTE]
    >Musíte vybrat fond hostitelů přidružený ke skupině aplikací. Skupiny aplikací mají aplikace nebo plochy, které jsou obsluhovány od hostitele relace, a hostitelé relace jsou součástí fondů hostitelů. Skupina aplikací musí být během vytváření přidružená ke fondu hostitelů.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky karty základy v Azure Portal.](media/basics-tab.png)

6. Pokud chcete přidat skupiny aplikací do fondu hostitelů, vyberte v nabídce na levé straně obrazovky **fondy hostitelů** .
   
    Pak vyberte název fondu hostitelů, do kterého chcete přidat skupiny aplikací.
   
    Potom v nabídce na levé straně obrazovky vyberte **skupiny aplikací** a pak vyberte **+ Přidat**.

    Nakonec vyberte skupinu předplatných a skupinu prostředků, ve které chcete vytvořit skupinu aplikací. Můžete buď vybrat název existující skupiny prostředků z rozevírací nabídky, nebo vybrat **vytvořit nové** a vytvořit novou.

      >[!NOTE]
      >Když do fondu hostitelů přidáte skupiny aplikací, fond hostitelů, který souvisí se skupinou aplikací, je už vybraný, protože jste na něj přešli.
      > 
      > [!div class="mx-imgBorder"]
      >![Snímek obrazovky s kartou základy s předvybraným fondem hostitelů.](media/host-pool-selected.png)

7. V části Typ skupiny aplikací vyberte **Vzdálená aplikace RemoteApp** a potom zadejte název vaší aplikace RemoteApp.

      > [!div class="mx-imgBorder"]
      > ![Snímek obrazovky s poli typu skupiny aplikací. Je zvýrazněna Vzdálená aplikace RemoteApp.](media/remoteapp-button.png)

8.  Vyberte kartu **přiřazení** .

9.  Pokud chcete publikovat jednotlivé uživatele nebo skupiny uživatelů ve skupině aplikací, vyberte **+ Přidat uživatele nebo skupiny uživatelů Azure AD**.

10.  Vyberte počet uživatelů, do kterých chcete přidat aplikace. Můžete vybrat jednoho nebo více uživatelů a skupin uživatelů.

     > [!div class="mx-imgBorder"]
     > ![Snímek obrazovky nabídky výběru uživatele](media/select-users.png)

11.  Vyberte **Vybrat**.

12.  Vyberte kartu **aplikace** a pak vyberte **+ Přidat aplikace**.

13.  Přidání aplikace z nabídky Start: 

      - Přejděte do části **zdroj aplikace** a v rozevírací nabídce vyberte **Nabídka Start** . Potom přejděte na **aplikace** a z rozevírací nabídky vyberte aplikaci.

     > [!div class="mx-imgBorder"]
     > ![Snímek obrazovky obrazovky Přidat aplikaci s vybranou nabídkou Start](media/add-app-start.png)

      - Do pole **Zobrazovaný název**zadejte název aplikace, který se zobrazí uživateli na svém klientovi.

      - Ostatní možnosti ponechte zapnuté a vyberte **Uložit**.

14. Přidání aplikace z konkrétní cesty k souboru:

      - Přejděte na **zdroj aplikace** a v rozevírací nabídce vyberte **cesta k souboru** .

      - Zadejte cestu k aplikaci na hostiteli relace, která je zaregistrovaná u přidruženého fondu hostitelů.

      - Zadejte podrobnosti aplikace do polí **název aplikace**, **Zobrazovaný název**, **cesta ikony**a **index ikony** .

      - Vyberte **Uložit**.

     > [!div class="mx-imgBorder"]
     > ![Snímek obrazovky se stránkou přidat aplikaci s vybranou cestou k souboru](media/add-app-file.png)

     Tento postup opakujte pro každou aplikaci, kterou chcete přidat do skupiny aplikací.

15.  V dalším kroku vyberte kartu **pracovní prostor** .

16.  Pokud chcete zaregistrovat skupinu aplikací k pracovnímu prostoru, klikněte na **zaregistrovat skupinu aplikací** a vyberte **Ano**. Pokud místo toho chcete skupinu aplikací zaregistrovat později, vyberte možnost **ne**.

17.  Pokud vyberete **Ano**, můžete vybrat existující pracovní prostor, do kterého se má skupina aplikací zaregistrovat.
       
       >[!NOTE]
       >Skupinu aplikací můžete zaregistrovat jenom k pracovním prostorům vytvořeným ve stejném umístění jako fond hostitelů. Lze. Pokud jste předtím zaregistrovali jinou skupinu aplikací ze stejného fondu hostitelů jako novou skupinu aplikací k pracovnímu prostoru, bude vybraná a nebude možné ji upravit. Všechny skupiny aplikací z fondu hostitelů musí být zaregistrované do stejného pracovního prostoru.

     > [!div class="mx-imgBorder"]
     > ![Snímek obrazovky se stránkou zaregistrovat skupinu aplikací pro už existující pracovní prostor Fond hostitelů je předvybraný.](media/register-existing.png)

18. Případně, pokud chcete vytvořit značky, které usnadňují uspořádání vašeho pracovního prostoru, vyberte kartu **značky** a zadejte názvy značek.

19. Až budete hotovi, vyberte kartu **Revize + vytvořit** .

20. Počkejte, než se proces ověření dokončí. Až to bude hotové, vyberte **vytvořit** a nasaďte skupinu aplikací.

Proces nasazení provede následující akce:

- Vytvořte skupinu aplikace RemoteApp.
- Přidejte vybrané aplikace do skupiny aplikací.
- Publikujte skupinu aplikací publikovanou pro uživatele a skupiny uživatelů, které jste vybrali.
- Pokud se rozhodnete tuto skupinu aplikací provést, zaregistrujte ji.
- Vytvořte odkaz na šablonu Azure Resource Manager na základě konfigurace, kterou si můžete stáhnout a uložit pro pozdější verzi.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit skupinu aplikací, naplnit ji v aplikacích RemoteApp a přiřazovat uživatele do skupiny aplikací. Další informace o tom, jak vytvořit fond ověřovacích hostitelů, najdete v následujícím kurzu. Pomocí fondu hostitelů ověření můžete monitorovat aktualizace služby před jejich vrácením do produkčního prostředí.

> [!div class="nextstepaction"]
> [Vytvoření fondu hostitelů pro ověření aktualizací služeb](./create-validation-host-pool.md)
