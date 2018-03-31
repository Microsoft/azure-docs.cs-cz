---
title: Správa konfigurace služby a profily | Microsoft Docs
description: Naučte se pracovat se soubory konfigurace profilů a konfigurace služby | které uložit nastavení pro nasazení prostředí a nastavení publikování pro cloudové služby.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 7da8c551-fb06-4057-b5c7-c77f4b39d803
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/11/2017
ms.author: ghogen
ms.openlocfilehash: 411daa8892bee1858c6930dfd8b2b811f164ec5d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-manage-service-configurations-and-profiles"></a>Správa konfigurace služby a profily
## <a name="overview"></a>Přehled
Při publikování cloudové služby Visual Studio ukládá informace o konfiguraci v dva druhy konfigurační soubory: služby konfigurace a profily. Konfigurace služby (soubory .cscfg) uložit nastavení pro nasazení prostředí pro cloudové služby Azure. Azure používá tyto konfigurační soubory, pokud spravuje vaše cloudové služby. Nastavení pro cloudové služby publikování na druhé straně úložiště profilů (.azurePubxml soubory). Tato nastavení jsou záznam co zvolíte, když použijete Průvodce Publikovat a jsou místně využívá sada Visual Studio. Toto téma vysvětluje, jak pracovat s oběma typy konfigurační soubory.

## <a name="service-configurations"></a>Konfigurace služby
Můžete vytvořit více konfigurace služby pro každé prostředí nasazení. Například může vytvořit konfiguraci služby pro místní prostředí, který používáte ke spuštění a testování aplikací Azure a jiné konfigurace služby pro produkční prostředí.

Můžete přidat, odstranění, přejmenování a upravit tyto konfigurace služby podle svých požadavků. Ze sady Visual Studio, můžete spravovat tyto konfigurace služby, jak je znázorněno na následujícím obrázku.

![Správa konfigurace služby](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

Můžete také otevřít **spravovat konfigurace** dialogové okno ze stránky vlastností role. Otevřete vlastnosti pro roli v projektu Azure, otevřete místní nabídku pro tuto roli a potom zvolte **vlastnosti**. Na **nastavení** rozbalte **konfigurace služby** seznamu a pak vyberte **spravovat** otevřete **spravovat konfigurace** dialogové okno.

### <a name="to-add-a-service-configuration"></a>K přidání konfigurace služby
1. V Průzkumníku řešení otevřete místní nabídky projektu Azure a pak vyberte **spravovat konfigurace**.
   
    **Konfigurace služby Správa** zobrazí se dialogové okno.
2. K přidání konfigurace služby, musíte vytvořit kopii stávající konfigurace. To pokud chcete udělat, zvolte konfiguraci, kterou chcete zkopírovat ze seznamu název a potom vyberte **vytvořit kopii**.
3. (Volitelné) Konfigurace služby jiný název, vyberte novou konfiguraci služby ze seznamu název a potom vyberte **přejmenovat**. V **název** textové pole, zadejte název, který chcete použít pro tuto konfiguraci služby a potom vyberte **OK**.
   
    Nové služby konfiguračního souboru, který je s názvem objekt ServiceConfiguration. [Nový název] .cscfg se přidá k projektu Azure v Průzkumníku řešení.

### <a name="to-delete-a-service-configuration"></a>Chcete-li odstranit konfiguraci služby
1. V Průzkumníku řešení otevřete místní nabídky projektu Azure a pak vyberte **spravovat konfigurace**.
   
    **Konfigurace služby Správa** zobrazí se dialogové okno.
2. Pokud chcete odstranit konfiguraci služby, zvolte konfiguraci, kterou chcete odstranit z **název** seznamu a pak vyberte **odebrat**. Chcete-li ověřit, že chcete odstranit tuto konfiguraci se zobrazí dialogové okno.
3. Vyberte **Odstranit**.
   
     Konfigurační soubor služby se odebere ze projektu Azure v Průzkumníku řešení.

### <a name="to-rename-a-service-configuration"></a>Chcete-li přejmenovat konfiguraci služby
1. V Průzkumníku řešení otevřete místní nabídky projektu Azure a pak vyberte **spravovat konfigurace**.
   
    **Konfigurace služby Správa** zobrazí se dialogové okno.
2. Přejmenování konfigurace služby, vyberte novou konfiguraci služby z **název** seznamu a pak vyberte **přejmenovat**. V **název** textové pole, zadejte název, který chcete použít pro tuto konfiguraci služby a potom vyberte **OK**.
   
    Název konfiguračního souboru služby se změnilo v projektu Azure v Průzkumníku řešení.

### <a name="to-change-a-service-configuration"></a>Chcete-li změnit konfiguraci služby
* Pokud chcete změnit konfiguraci služby, otevřete místní nabídku pro určité role, kterou chcete změnit v projektu Azure a pak vyberte **vlastnosti**. V tématu [postupy: Konfigurace rolí pro cloudové služby Azure pomocí sady Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) Další informace.

## <a name="make-different-setting-combinations-by-using-profiles"></a>Zkontrolujte nastavení různé kombinace s použitím profilů
Pomocí profilu můžete automaticky vyplnit **Průvodci publikováním** nabízela jinou kombinaci nastavení pro jiné účely. Například může mít jeden profil pro ladění a druhý pro verzi sestavení. V takovém případě vaše **ladění** profil by měla mít **IntelliTrace** povolená a **ladění** konfigurace vybrali a **verze** profil by měla mít **IntelliTrace** zakázána a **verze** konfigurace vybraná. Různé profily můžete také použít k nasazení služby pomocí jiný účet úložiště.

Když poprvé spustíte průvodce, vytvoří se výchozí profil. Visual Studio ukládá profil v souboru, který má příponu .azurePubXml, která se přidá do projektu Azure v části **profily** složky. Pokud ručně zadáte různé možnosti, když spustíte Průvodce později, soubor se automaticky aktualizuje. Před spuštěním následujícího postupu, měli jste již publikovali cloudové služby alespoň jednou.

### <a name="to-add-a-profile"></a>Přidání profilu
1. Otevřete místní nabídky projektu Azure a pak vyberte **publikovat**.
2. Vedle položky **cíle profil** seznamu, vyberte **uložit profil** tlačítko, jak ukazuje následující obrázek. Tím se vytvoří profil pro vás.
   
    ![Vytvořit nový profil](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)
3. Po vytvoření profilu vyberte **< spravovat... >** v **cíle profil** seznamu.
   
    **Spravovat profily** se zobrazí dialogové okno, jak ukazuje následující obrázek.
   
    ![Dialogové okno profily spravovat](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)
4. V **název** seznam, vyberte profil a pak vyberte **vytvořit kopii**.
5. Vyberte **Zavřít** tlačítko.
   
    Nový profil se zobrazí v seznamu cíl profilu.
6. V **cíle profil** seznamu, vyberte profil, který jste právě vytvořili. Nastavení Průvodce publikování se vyplní volby z profilu, který jste vybrali.
7. Vyberte **předchozí** a **Další** tlačítka na každé stránce Průvodce publikování zobrazit a pak přizpůsobit nastavení pro tento profil. V tématu [Průvodci publikováním aplikace Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) informace.
8. Po dokončení přizpůsobení nastavení, vyberte **Další** se vrátíte na stránku nastavení. Profil je uložen při publikování služby pomocí těchto nastavení, nebo pokud jste vybrali **Uložit** vedle seznamu profilů.

### <a name="to-rename-or-delete-a-profile"></a>Přejmenování nebo odstranění profilu
1. Otevřete místní nabídky projektu Azure a pak vyberte **publikovat**.
2. V **cíle profil** seznamu, vyberte **spravovat**.
3. V **spravovat profily** dialogové okno, vyberte profil, který chcete odstranit a potom vyberte **odebrat**.
4. V dialogovém okně potvrzení vyberte **OK**.
5. Vyberte **Zavřít**.

### <a name="to-change-a-profile"></a>Chcete-li změnit profil
1. Otevřete místní nabídky projektu Azure a pak vyberte **publikovat**.
2. V **cíle profil** seznamu, vyberte profil, který chcete změnit.
3. Vyberte **předchozí** a **Další** tlačítka zobrazovat každé stránce Průvodce publikování, a poté změňte nastavení chcete. V tématu [Průvodci publikováním aplikace Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) informace.
4. Po dokončení změn nastavení, vyberte **Další** se vrátíte k **nastavení** stránky.
5. (Volitelné) vyberte **publikovat** publikovat cloudové služby pomocí nového nastavení. Pokud se nechcete publikovat cloudové služby v tuto chvíli a zavřít průvodce publikovat, Visual Studio se zobrazí, pokud chcete uložit změny do profilu.

## <a name="next-steps"></a>Další postup
Další informace o konfiguraci dalších částí projektu Azure ze sady Visual Studio najdete v tématu [konfigurace projektu Azure](http://go.microsoft.com/fwlink/p/?LinkID=623075)

