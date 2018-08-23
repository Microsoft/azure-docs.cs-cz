---
title: Publikování cloudové služby pomocí nástroje Azure | Dokumentace Microsoftu
description: Další informace o tom, jak publikovat projekty služeb v cloudu Azure pomocí sady Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 1a07b6e4-3678-4cbf-b37e-4520b402a3d9
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: 78a313e824ee7926ab9f1875407d650d614afce2
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054436"
---
# <a name="publishing-a-cloud-service-using-visual-studio"></a>Publikování cloudové služby pomocí sady Visual Studio

Visual Studio můžete publikovat aplikaci přímo do Azure s podporou pro pracovní a provozní prostředí cloudové služby. Při publikování, vyberte prostředí pro nasazení a účet úložiště, který se používá dočasně balíčku pro nasazení.

Při vývoji a testování aplikací Azure, můžete použít Web Deploy a publikujte změny postupně pro webové role. Po publikování aplikace do prostředí nasazení, nasazení webu umožňuje změny nasadí přímo do virtuálního počítače, na kterém běží ve webové roli. Není nutné balíček a publikujte celou aplikaci Azure pokaždé, když chcete aktualizovat vaši webovou roli a otestujte provedené změny. Díky tomuto přístupu může mít změny webové role k dispozici v cloudu pro testování bez čekání na máte svou aplikaci publikovat do prostředí nasazení.

Aplikaci Azure publikovat a aktualizovat webové role pomocí nasazení webu pomocí následujících postupů:

- Publikování nebo balení aplikací Azure ze sady Visual Studio
- Aktualizace webové role v rámci vývojové a testovací cyklus

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Publikování nebo balení aplikací Azure ze sady Visual Studio

Když publikujete aplikaci Azure, je provést jednu z následujících úloh:

- Vytvořit balíček služby: Tento balíček a konfigurační soubor služby můžete použít k publikování aplikace do prostředí nasazení z [webu Azure portal](https://portal.azure.com).

- Publikování projektu Azure v sadě Visual Studio: můžete publikovat svoji aplikaci přímo do Azure, můžete použít Průvodce publikováním. Informace najdete v tématu [Průvodce publikováním aplikace Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Chcete-li vytvořit balíček služby ze sady Visual Studio

1. Když budete chtít aplikaci publikovat, otevřete Průzkumníka řešení, otevřete místní nabídku pro projekt Azure, který obsahuje vaše role a vyberte publikovat.

1. Chcete-li vytvořit balíček služby pouze, postupujte takto:

   a. Na místní nabídku pro projekt Azure, zvolte **balíčku**.

   b. V **instalace balíčku Azure** dialogovém okně vyberte konfiguraci služby, pro kterou chcete vytvořit balíček a pak zvolte konfiguraci sestavení.

   c. (Volitelné) Chcete-li zapnout vzdálené plochy pro cloudovou službu po publikování, vyberte **povolit vzdálenou plochu pro všechny role**a pak vyberte **nastavení** konfigurace přihlašovacích údajů vzdálené plochy. Další informace najdete v tématu [povolit připojení ke vzdálené ploše pro roli v cloudových službách Azure pomocí sady Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

      Pokud chcete ladit vaši cloudovou službu, jakmile ji publikujete, zapnout vzdálené ladění tak, že vyberete **povolit vzdálený ladicí program pro všechny role**.

   d. Chcete-li vytvořit balíček, zvolte **balíčku** odkaz.

      Průzkumník souborů zobrazuje umístění souboru nově vytvořený balíček. Toto umístění můžete zkopírovat tak, aby ho můžete používat na webu Azure Portal.

   e. Pokud chcete publikovat tento balíček pro nasazení prostředí, musíte použít toto umístění jako umístění balíčku při vytváření cloudové služby a nasazení tohoto balíčku do prostředí pomocí webu Azure portal.

1. (Volitelné) Chcete-li zrušit proces nasazení, v místní nabídce pro položky v protokolu aktivit, zvolte **zrušit a odebrat**. Tento příkaz celý proces nasazení zastaví a odstraní prostředí nasazení z Azure. Chcete-li odebrat prostředí po nasazení, pomocí webu Azure portal.

1. (Volitelné) Po spuštění mají vaše instance rolí, sada Visual Studio automaticky zobrazí prostředí nasazení se **Cloud Services** uzlu v Průzkumníku serveru. Tady se zobrazí stav instance jednotlivých rolí. Zobrazit [prostředky správy Azure pomocí Průzkumníka cloudu](vs-azure-tools-resources-managing-with-cloud-explorer.md). Následující obrázek znázorňuje instance rolí, zatímco jsou stále ve stavu Probíhá inicializace:

    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Aktualizace webové role v rámci vývojové a testovací cyklus

Pokud je vaše aplikace back-end infrastrukturu stabilní, ale webové role vyžadují více časté aktualizace, můžete použít Web Deploy aktualizovat jenom webové role v projektu. Nástroj nasazení webu je užitečné, když nechcete, aby znovu sestavte a znovu nasadit role pracovního procesu back-endu, nebo pokud máte více webových rolí a chcete aktualizovat pouze jeden z webové role.

### <a name="requirements-for-using-web-deploy"></a>Požadavky pro pomocí nasazení webu

- **Pouze pro vývoj a testování účely**: změny probíhají přímo k virtuálnímu počítači se spuštěným ve webové roli. Pokud tento virtuální počítač má k provedení recyklace, změny budou ztraceny, protože původní balíček, kterou jste publikovali se používá pro opětovné vytvoření virtuálního počítače pro roli. Znovu publikujte aplikaci pro získání nejnovějších změn pro webovou roli.

- **Je možné aktualizovat jenom webové role**: role pracovního procesu se nedá aktualizovat. Kromě toho nelze aktualizovat `RoleEntryPoint` v `web role.cs`.

- **Jedna instance webové role podporují jenom**: nelze mít více instancí žádné webové role v prostředí pro nasazení. Více webových rolí každý jenom s jednou instancí jsou však podporovány.

- **Povolit připojení ke vzdálené ploše**: Tento požadavek umožňuje Web Deploy používat uživatele a heslo pro připojení k virtuálnímu počítači, který chcete nasadit změny na server, na kterém běží Internetové informační služby (IIS). Kromě toho můžete potřebovat pro připojení k virtuálnímu počítači přidat důvěryhodný certifikát do služby IIS na tomto virtuálním počítači. (Tento certifikát zajišťuje, že vzdáleného připojení pro službu IIS, který používá Webdeploy zabezpečené.)

Následující postup předpokládá, že používáte **publikování aplikaci Azure** průvodce.

### <a name="enable-web-deploy-when-you-publish-your-application"></a>Povolit při publikování aplikace, nasazení webu

1. Povolit **povolit nasazení webu pro všechny webové role** možnost, je nutné nejprve nakonfigurovat připojení ke vzdálené ploše. Vyberte **povolit vzdálenou plochu** u všech rolí a pak zadejte přihlašovací údaje, které slouží k připojení vzdáleně v **konfigurace vzdálené plochy** pole, které se zobrazí. Zobrazit [povolit připojení ke vzdálené ploše pro roli v cloudových službách Azure pomocí sady Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

1. Pokud chcete povolit nasazení webu pro webové role ve vaší aplikaci, vyberte **povolit nasazení webu pro všechny webové role**.

    Žlutý trojúhelník upozornění se zobrazí. Nástroj nasazení webu používá ve výchozím nastavení, což se nedoporučuje pro nahrávání citlivých dat nedůvěryhodný certifikát podepsaný svým držitelem. Pokud je potřeba zabezpečit tento proces pro citlivá data, můžete přidat certifikát SSL pro Web Deploy připojení. Tento certifikát musí být důvěryhodný certifikát. Další informace najdete v tématu [zabezpečit webové nasazení](#make-web-deploy-secure).

1. Zvolte **Další** zobrazíte **Souhrn** obrazovku a klikněte na tlačítko **publikovat** nasadit cloudovou službu.

    Cloudové služby je publikována. Virtuální počítač, který je vytvořen má povoleno pro službu IIS tak, aby nasazení webu je možné aktualizovat webové role bez nutnosti jejich publikování vzdálené připojení.

   > [!NOTE]
   > Pokud máte více než jednu instanci nakonfigurovanou pro webové role, zobrazí se zpráva upozornění, s informacemi o tom, že je každá webová role omezená na jednu instanci pouze v balíčku, který je vytvořen pro publikování vaší aplikace. Pokračujte výběrem tlačítka **OK**. Jak je uvedeno v části požadavky, můžete mít více než jednu webovou roli, ale pouze jedna instance každé role.

### <a name="update-your-web-role-by-using-web-deploy"></a>Aktualizovat vaši webovou roli pomocí nasazení webu

1. Službu Web Deploy používat, provádět změny kódu do projektu pro všechny vaše webové role v aplikaci Visual Studio, kterou chcete publikovat a pak klikněte pravým tlačítkem na tento uzel projektu v rámci vašeho řešení a přejděte na **publikovat**. **Publikování webu** zobrazí se dialogové okno.

1. (Volitelné) Pokud jste přidali důvěryhodný certifikát SSL pro vzdálená připojení pro službu IIS, můžete zrušit **povolit certifikát nedůvěryhodného** zaškrtávací políčko. Informace o tom, jak přidat certifikát do nasazení webu zabezpečit, najdete v části **k Ujistěte se, webové nasazení zabezpečené** dále v tomto článku.

1. Mechanismus publikování pro službu Web Deploy používat, musí uživatelské jméno a heslo, které jste nastavili pro vaše připojení ke vzdálené ploše při prvním publikování balíčku.

   a. V **uživatelské jméno**, zadejte uživatelské jméno.

   b. V **heslo**, zadejte heslo.

   c. (Volitelné) Pokud chcete uložit toto heslo v tomto profilu, zvolte **uložit heslo**.

1. Chcete-li publikovat změny do vaší webové role, zvolte **publikovat**.

    Ve stavovém řádku zobrazí **publikování zahájeno**. Po dokončení publikování **publikování bylo úspěšné** se zobrazí. Změny jsou nyní nasazené webové role ve vašem virtuálním počítači. Nyní můžete spustit aplikaci Azure v prostředí Azure a otestujte provedené změny.

### <a name="make-web-deploy-secure"></a>Zabezpečit webové nasazení

1. Nástroj nasazení webu používá ve výchozím nastavení, což se nedoporučuje pro nahrávání citlivých dat nedůvěryhodný certifikát podepsaný svým držitelem. Pokud je potřeba zabezpečit tento proces pro citlivá data, můžete přidat certifikát SSL pro Web Deploy připojení. Tento certifikát musí být důvěryhodný certifikát, který můžete získat od certifikační autority (CA).

    Chcete-li zabezpečit nasazení webu pro každý virtuální počítač pro každou z webových rolí, musíte nahrát důvěryhodný certifikát, který chcete použít pro webové nasazení na webu Azure portal. Tento certifikát zajišťuje, že je certifikát přidat k virtuálnímu počítači, který je vytvořen pro webovou roli při publikování aplikace.

1. Přidání důvěryhodný certifikát SSL služby IIS pro vzdálené připojení používat, postupujte podle těchto kroků:

   a. Pro připojení k virtuálnímu počítači, na kterém běží ve webové roli, vyberte instance webové role v **Průzkumníka cloudu** nebo **Průzkumníka serveru**a klikněte na tlačítko **připojit pomocí vzdálené plochy**  příkazu. Podrobné pokyny o tom, jak se připojit k virtuálnímu počítači najdete v tématu [povolit připojení ke vzdálené ploše pro roli v cloudových službách Azure pomocí sady Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md). Prohlížeč zobrazí výzvu ke stažení `.rdp` souboru.

   b. Chcete-li přidat certifikát SSL, otevřete službu pro správu ve Správci služby IIS. Ve Správci služby IIS povolit protokol SSL tak, že otevřete **vazby** propojit **akce** podokně. **Přidat vazbu webu** zobrazí se dialogové okno. Zvolte **přidat**a klikněte na tlačítko HTTPS v **typ** rozevíracího seznamu. V **certifikát SSL** , zvolte certifikát SSL, aby měl podepsaný Certifikační autoritou a že jste nahráli na portál Azure Portal. Další informace najdete v tématu [konfigurace nastavení připojení pro službu Management](http://go.microsoft.com/fwlink/?LinkId=215824).

      > [!NOTE]
      > Pokud chcete přidat důvěryhodný certifikát SSL, žlutý trojúhelník upozornění již nebude zobrazen v **Průvodce publikováním**.

## <a name="include-files-in-the-service-package"></a>Zahrnout soubory v balíčku služby

Možná budete muset zahrnout konkrétní soubory v balíčku služby, aby byly k dispozici na virtuálním počítači, který je vytvořen pro roli. Například můžete chtít přidat `.exe` nebo `.msi` souboru, který je používán spouštěcí skript do vašeho balíčku služby. Nebo může být nutné přidat sestavení, která vyžaduje projekt webové role nebo pracovní role. Soubory k zahrnutí, je nutné je přidat do řešení pro vaše aplikace Azure.

1. K přidání sestavení do balíčku služby, použijte následující kroky:

   a. V **Průzkumníka řešení**, otevřete uzel projektu pro projekt, který chybí odkazovaná sestavení.
   b. Sestavení přidejte do projektu, otevřete místní nabídku **odkazy** složky a klikněte na tlačítko **přidat odkaz**. Zobrazí se dialogové okno Přidat odkaz.
   c. Zvolte odkaz, který chcete přidat a klikněte na tlačítko **OK**. Odkaz se přidá do seznamu **odkazy** složky.
   d. Otevřete místní nabídku pro sestavení, které jste přidali a zvolte **vlastnosti**. **Vlastnosti** zobrazí se okno.

      Zahrnout toto sestavení v balíčku služby **místní kopii seznamu** zvolte **True**.
1. V **Průzkumníka řešení** otevřete uzel projektu pro projekt, který chybí odkazovaná sestavení.

1. Sestavení přidejte do projektu, otevřete místní nabídku **odkazy** složky a klikněte na tlačítko **přidat odkaz**. **Přidat odkaz** se zobrazí dialogové okno.

1. Zvolte odkaz, který chcete přidat a pak klikněte **OK** tlačítko.

    Odkaz se přidá do seznamu **odkazy** složky.

1. Otevřete místní nabídku pro sestavení, které jste přidali a zvolte **vlastnosti**. Zobrazí se okno Vlastnosti.

1. Zahrnout toto sestavení v balíčku služby **Kopírovat místně** klikněte na položku **True**.

1. Chcete-li zahrnout soubory v balíčku služby, které byly přidány do projektu webové role, otevřete místní nabídku pro soubor a zvolte **vlastnosti**. Z **vlastnosti** okně zvolte **obsahu** z **akce sestavení** pole se seznamem.

1. Chcete-li zahrnout soubory v balíčku služby, které byly přidány do projektu role pracovního procesu, otevřete místní nabídku pro soubor a zvolte **vlastnosti**. Z **vlastnosti** okně zvolte **kopírovat, pokud je novější** z **kopírovat do výstupního adresáře** pole se seznamem.

## <a name="next-steps"></a>Další postup

Další informace o publikování do Azure ze sady Visual Studio, naleznete v tématu [Průvodce publikováním aplikace Azure](vs-azure-tools-publish-azure-application-wizard.md).
