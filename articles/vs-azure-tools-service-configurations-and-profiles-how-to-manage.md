---
title: Jak spravovat konfigurace služeb a profily | Dokumentace Microsoftu
description: Zjistěte, jak pracovat se soubory konfigurace profilů a konfigurace služby | které ukládání nastavení prostředí nasazení a nastavení publikování pro cloudové služby.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 7da8c551-fb06-4057-b5c7-c77f4b39d803
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/11/2017
ms.author: ghogen
ms.openlocfilehash: 06ec029d1978488649d9b1e6f8a68f8ab5179f04
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969353"
---
# <a name="how-to-manage-service-configurations-and-profiles"></a>Jak spravovat konfigurace služeb a profily
## <a name="overview"></a>Přehled
Při publikování cloudové služby Visual Studio ukládá informace o konfiguraci dva druhy konfigurační soubory: Konfigurace a profily. Konfigurace služby (.cscfg soubory) ukládat nastavení prostředí nasazení pro cloudovou službu Azure. Při správě služby cloud services, Azure využívá tyto konfigurační soubory. Na druhé straně profily (soubory .azurePubxml) úložiště nastavení publikování pro cloudové služby. Tato nastavení jsou záznamy o volbách, když použijete Průvodce publikováním a používají místně pomocí sady Visual Studio. Toto téma vysvětluje, jak pracovat s oběma typy konfiguračních souborů.

## <a name="service-configurations"></a>Konfigurace služby
Můžete vytvořit více konfigurace služby pro použití u každého prostředí nasazení. Například může vytvořit konfiguraci služby pro místní prostředí, který používáte ke spuštění a testování aplikací Azure a jinou konfiguraci služby pro vaše produkční prostředí.

Můžete přidat, odstranit, přejmenovat a změnit tyto konfigurace služeb na základě vašich požadavků. Ze sady Visual Studio, můžete spravovat tyto konfigurace služby, jak je znázorněno na následujícím obrázku.

![Spravovat konfigurace služeb](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

Můžete také otevřít **spravovat konfigurace** dialogové okno stránky vlastností této role. Otevřete vlastnosti pro roli v projektu Azure, otevřete místní nabídku pro tuto roli a klikněte na tlačítko **vlastnosti**. Na **nastavení** kartu, rozbalte **konfigurace služby** seznamu a pak vyberte **spravovat** otevřít **spravovat konfigurace** Dialogové okno.

### <a name="to-add-a-service-configuration"></a>Chcete-li přidat konfiguraci služby
1. V Průzkumníku řešení otevřete místní nabídku pro projekt Azure a pak vyberte **spravovat konfigurace**.
   
    **Spravovat konfigurace služeb** zobrazí se dialogové okno.
2. Chcete-li přidat konfiguraci služby, musíte vytvořit kopii existující konfigurace. K tomuto účelu zvolte konfiguraci, kterou chcete zkopírovat ze seznamu název a potom vyberte **vytvořit kopii**.
3. (Volitelné) Aby byla konfigurace služby jiný název, vyberte novou konfiguraci služby ze seznamu název a pak vyberte **přejmenovat**. V **název** textové pole, zadejte název, který chcete použít pro tuto konfiguraci služby a potom vyberte **OK**.
   
    Nová služba konfigurační soubor s názvem ServiceConfiguration. [Název] .cscfg je přidán do projektu Azure v Průzkumníku řešení.

### <a name="to-delete-a-service-configuration"></a>Odstranit konfiguraci služby
1. V Průzkumníku řešení otevřete místní nabídku pro projekt Azure a pak vyberte **spravovat konfigurace**.
   
    **Spravovat konfigurace služeb** zobrazí se dialogové okno.
2. Chcete-li odstranit konfiguraci služby, zvolte konfiguraci, kterou chcete odstranit z **název** seznamu a pak vyberte **odebrat**. Chcete-li ověřit, že chcete odstranit tuto konfiguraci se zobrazí dialogové okno.
3. Vyberte **Odstranit**.
   
     Konfigurační soubor služby se odebere z projektu Azure v Průzkumníku řešení.

### <a name="to-rename-a-service-configuration"></a>Chcete-li přejmenovat konfigurace služby
1. V Průzkumníku řešení otevřete místní nabídku pro projekt Azure a pak vyberte **spravovat konfigurace**.
   
    **Spravovat konfigurace služeb** zobrazí se dialogové okno.
2. Chcete-li přejmenovat konfigurace služby, zvolte novou konfiguraci služby z **název** seznamu a pak vyberte **přejmenovat**. V **název** textové pole, zadejte název, který chcete použít pro tuto konfiguraci služby a potom vyberte **OK**.
   
    Název konfiguračního souboru služby se změnilo v projektu Azure v Průzkumníku řešení.

### <a name="to-change-a-service-configuration"></a>Chcete-li změnit konfiguraci služby
* Pokud chcete změnit konfiguraci služby, otevřete místní nabídku pro konkrétní role, které chcete změnit v projektu Azure a pak vyberte **vlastnosti**. V tématu [postupy: Konfigurace rolí pro cloudové služby Azure pomocí sady Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) Další informace.

## <a name="make-different-setting-combinations-by-using-profiles"></a>Ujistěte se, kombinace různých nastavení s použitím profilů
Když použijete profil, můžete automaticky vyplnit **Průvodce publikováním** s různými kombinacemi nastavení pro různé účely. Například může mít jeden profil pro ladění a další položku pro verzi sestavení. V takovém případě vaše **ladění** profilu bude mít **IntelliTrace** povolené a **ladění** vybranou, konfiguraci a **vydání** profil by obsahovat **IntelliTrace** zakázána a **vydání** vybranou konfiguraci. Různé profily můžete také použít k nasazení služby pomocí jiného účtu úložiště.

Při prvním spuštění průvodce, vytvoří se výchozí profil. Visual Studio uloží profil v souboru s příponou .azurePubXml, která se přidá do projektu Azure v rámci **profily** složky. Pokud ručně zadáte různých možností při spuštění Průvodce později, soubor se automaticky aktualizuje. Před spuštěním následujícího postupu by jste již publikovali cloudové služby alespoň jednou.

### <a name="to-add-a-profile"></a>Chcete-li přidat profil
1. Otevřete místní nabídku pro projekt Azure a pak vyberte **publikovat**.
2. Vedle položky **cílový profil** seznamu, vyberte **uložit profil** tlačítko, jako je vidět na následujícím obrázku. Tím se vytvoří profil, který pro vás.
   
    ![Vytvořit nový profil](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)
3. Po vytvoření profilu vyberte **< spravovat >** v **cílový profil** seznamu.
   
    **Spravovat profily** zobrazí se dialogové okno, jako je vidět na následujícím obrázku.
   
    ![Spravovat profily dialogového okna](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)
4. V **název** seznamu, zvolte profil a potom vyberte **vytvořit kopii**.
5. Zvolte **Zavřít** tlačítko.
   
    Nový profil se zobrazí v seznamu cílů profilu.
6. V **cílový profil** seznamu, vyberte profil, který jste právě vytvořili. Nastavení Průvodce publikování se vyplní možností profil, který jste vybrali.
7. Vyberte **předchozí** a **Další** tlačítka pro zobrazení jednotlivých stránkách Průvodce publikováním a nastavení pro tento profil. Zobrazit [Průvodce publikováním aplikace Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) informace.
8. Po dokončení úprav nastavení, vyberte **Další** přejděte zpět na stránku nastavení. Profil je uložen při publikování služby pomocí těchto nastavení nebo pokud vyberete **Uložit** vedle seznamu profilů.

### <a name="to-rename-or-delete-a-profile"></a>Přejmenování nebo odstranění profilu
1. Otevřete místní nabídku pro projekt Azure a pak vyberte **publikovat**.
2. V **cílový profil** seznamu vyberte **spravovat**.
3. V **spravovat profily** dialogové okno, vyberte profil, který chcete odstranit a potom vyberte **odebrat**.
4. V dialogovém okně potvrzení vyberte **OK**.
5. Vyberte **Zavřít**.

### <a name="to-change-a-profile"></a>Chcete-li změnit profil
1. Otevřete místní nabídku pro projekt Azure a pak vyberte **publikovat**.
2. V **cílový profil** seznamu, vyberte profil, který chcete změnit.
3. Vyberte **předchozí** a **Další** tlačítka pro zobrazení jednotlivých stránkách Průvodce publikováním a potom změňte požadovaná nastavení. Zobrazit [Průvodce publikováním aplikace Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) informace.
4. Po dokončení změny nastavení, vyberte **Další** chcete přejít zpátky k **nastavení** stránky.
5. (Volitelné) vyberte **publikovat** k publikování cloudové služby pomocí nového nastavení. Pokud nechcete publikovat vaše Cloudová služba v tuto chvíli a zavřete průvodce publikovat, Visual Studio výzvu, pokud chcete uložit změny do profilu.

## <a name="next-steps"></a>Další postup
Další informace o konfigurování dalších součástí projektu Azure v sadě Visual Studio najdete v tématu [konfigurace projektu aplikace Azure](http://go.microsoft.com/fwlink/p/?LinkID=623075)

