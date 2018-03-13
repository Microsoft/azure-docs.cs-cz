---
title: "Publikování cloudové služby pomocí nástroje Azure | Microsoft Docs"
description: "Další informace o tom, jak publikovat projekty Azure cloudových služeb pomocí sady Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 1a07b6e4-3678-4cbf-b37e-4520b402a3d9
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2017
ms.author: kraigb
ms.openlocfilehash: e854c46329f092388531a73ada50829c4579806d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="publishing-a-cloud-service-using-visual-studio"></a>Publikování cloudové služby pomocí sady Visual Studio

Visual Studio můžete publikovat aplikaci přímo do Azure, se podpora pro pracovní a provozní prostředí cloudové služby. Při publikování, vyberte nasazení prostředí a účet úložiště, který je použit dočasně balíčku pro nasazení.

Pokud jste vývoj a testování aplikací Azure, můžete použít nasazení webu k publikování změny přírůstkově pro webové role. Po publikování aplikace do prostředí nasazení, nasazení webu umožňuje nasadit změny přímo na virtuální počítač, který běží webové role. Nemáte balíčku a publikování aplikace celý Azure pokaždé, když chcete aktualizovat vaši webovou roli k otestování změny. S tímto přístupem může mít webové role změny k dispozici v cloudu pro testování bez čekání na vaše aplikace publikována do prostředí nasazení.

K publikování aplikace Azure a k aktualizaci webové role pomocí nástroje nasazení webu pomocí následujících postupů:

- Publikování nebo balíčků aplikací Azure ze sady Visual Studio
- Aktualizovat webovou roli jako součást vývoj a testování cyklu

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Publikování nebo balíčků aplikací Azure ze sady Visual Studio

Když publikujete aplikaci Azure, můžete provést jednu z následujících úloh:

- Vytvořit balíček služby: Tento balíček a konfigurační soubor služby můžete použít k publikování aplikace do prostředí nasazení z [portál Azure](https://portal.azure.com).

- Publikování projektu Azure ze sady Visual Studio: pro publikování aplikace přímo do Azure, můžete použít Průvodce publikování. Informace najdete v tématu [Průvodci publikováním aplikace Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Chcete-li vytvořit balíček služby ze sady Visual Studio

1. Jakmile budete připraveni k publikování aplikace, otevřete Průzkumníka řešení, otevřete místní nabídku pro Azure projekt, který obsahuje vaše role a volba možnosti publikovat.

1. Chcete-li vytvořit balíček služby pouze, postupujte takto:

   a. V místní nabídce pro projektu Azure, zvolte **balíček**.

   b. V **balíčku aplikace Azure** dialogové okno, zvolte konfigurace služby, pro který chcete vytvořit balíček a potom je konfigurace sestavení.

   c. (Volitelné) Chcete-li na vzdálené plochy pro cloudové služby po publikování, vyberte **povolení vzdálené plochy u všech rolí**a potom vyberte **nastavení** konfigurace pověření pro připojení ke vzdálené ploše. Další informace najdete v tématu [povolit připojení ke vzdálené ploše pro roli ve službě Azure Cloud Services pomocí sady Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

      Pokud chcete ladit cloudové služby, jakmile ji publikujete, zapnout vzdálené ladění výběrem **povolení vzdáleného ladicího programu u všech rolí**.

   d. Chcete-li vytvořit balíček, klikněte **balíček** odkaz.

      Průzkumník souborů ukazuje umístění souboru nově vytvořený balíčku. Toto umístění můžete zkopírovat, aby ho můžete používat z portálu Azure.

   e. Pokud chcete publikovat tento balíček do prostředí nasazení, musíte použít toto umístění jako umístění balíčku při vytváření cloudové služby a nasazení tohoto balíčku do prostředí pomocí portálu Azure.

1. (Volitelné) Chcete-li zrušit procesu nasazení v místní nabídce řádku položky v protokolu aktivit, zvolte **zrušit a odeberte**. Tento příkaz zastaví proces nasazení a odstraní prostředí nasazení z Azure. Je možné odebrat prostředí po nasazení pomocí portálu Azure.

1. (Volitelné) Po spuštění instance role mít Visual Studio automaticky zobrazí prostředí nasazení v **cloudové služby** uzlu v Průzkumníku serveru. Zde se zobrazí stav instance jednotlivých rolí. V tématu [Správa Azure prostředků pomocí Průzkumníka cloudu](vs-azure-tools-resources-managing-with-cloud-explorer.md). Následující obrázek znázorňuje instance rolí v době, kdy jsou stále ve stavu Probíhá inicializace:

    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Aktualizovat webovou roli jako součást vývoj a testování cyklu

Pokud je vaše aplikace back-end infrastruktura stabilní, ale webové role třeba více často aktualizovat, můžete použít Web Deploy aktualizovat webovou roli ve vašem projektu. Nasazení webu je užitečné, když nechcete znovu sestavili a nasadili rolí pracovního procesu back-end, nebo pokud máte více webové role a chcete aktualizovat pouze jeden z webové role.

### <a name="requirements-for-using-web-deploy"></a>Požadavky pro použití nástroje nasazení webu

- **Pouze pro vývoj a testování účely**: provedení změn přímo k virtuálnímu počítači se spuštěným systémem webovou roli. Pokud tento virtuální počítač má recyklace, změny budou ztraceny, protože původní balíček, který jste publikovali se používá k opětovnému vytvoření virtuálního počítače pro roli. Publikujte aplikaci získání nejnovějších změn pro webovou roli znovu.

- **Aktualizovat lze pouze webové role**: nelze aktualizovat rolí pracovního procesu. Kromě toho nelze aktualizovat `RoleEntryPoint` v `web role.cs`.

- **Podporuje pouze jednu instanci webové role**: nelze mít více instancí jakoukoli webovou roli v prostředí pro nasazení. Víc webových rolí každý se pouze jedna instance jsou však podporovány.

- **Povolit připojení ke vzdálené ploše**: Tento požadavek umožňuje Web Deploy používat uživatel a heslo pro připojení k virtuálnímu počítači k nasazení změny na server, který běží Internetové informační služby (IIS). Kromě toho budete muset připojit k virtuálnímu počítači do IIS přidat důvěryhodný certifikát na tomto virtuálním počítači. (Tento certifikát zajišťuje, že je zabezpečeného vzdáleného připojení pro službu IIS, který je používán nasazení webu.)

V následujícím postupu se předpokládá, že používáte **publikování aplikaci Azure** průvodce.

### <a name="enable-web-deploy-when-you-publish-your-application"></a>Povolit nasazení webu při publikování aplikace

1. Chcete-li povolit **povolit nasazení webu pro všechny webové role** možnost, musíte nejdřív nakonfigurovat připojení ke vzdálené ploše. Vyberte **povolení vzdálené plochy** u všech rolí a pak zadejte přihlašovací údaje, které se používá k připojení vzdáleně v **konfigurace vzdálené plochy** pole, které se zobrazí. V tématu [povolit připojení ke vzdálené ploše pro roli ve službě Azure Cloud Services pomocí sady Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

1. Chcete-li nasazení webu pro všechny webové role v aplikaci, vyberte **povolit nasazení webu pro všechny webové role**.

    Žlutý trojúhelník upozornění se zobrazí. Nasazení webu používá certifikát podepsaný svým držitelem, nedůvěryhodné ve výchozím nastavení, která se nedoporučuje pro nahrávání citlivá data. Pokud potřebujete zabezpečit tento proces pro citlivá data, můžete přidat certifikát SSL, který se má použít pro nasazení webu připojení. Tento certifikát musí být důvěryhodný certifikát. Další informace najdete v tématu [zabezpečit webové nasazení](#make-web-deploy-secure).

1. Zvolte **Další** zobrazíte **Souhrn** obrazovce a potom vyberte **publikovat** nasazení cloudové služby.

    Cloudové služby je publikován. Virtuální počítač, který je vytvořen má povolené pro službu IIS tak, aby nasazení webu, můžete použít k aktualizaci webové role znovu publikovat je vzdálená připojení.

   > [!NOTE]
   > Pokud máte více než jednu instanci nakonfigurována pro webovou roli, zobrazí se zpráva upozornění oznamující, že každý webovou roli je omezená na jednu instanci pouze v balíčku, který se vytvoří pro publikování aplikace. Pokračujte výběrem tlačítka **OK**. Jak je uvedeno v části požadavky, můžete mít více než jednu webovou roli, ale jenom jednu instanci každé role.

### <a name="update-your-web-role-by-using-web-deploy"></a>Aktualizovat vaši webovou roli pomocí nástroje nasazení webu

1. Službu Web Deploy používat, provádět změny kódu do projektu pro některý z vaší webové role v sadě Visual Studio, který chcete publikovat a potom klikněte pravým tlačítkem na tento uzel projektu ve vašem řešení a přejděte na příkaz **publikovat**. **Publikovat Web** zobrazí se dialogové okno.

1. (Volitelné) Pokud jste přidali důvěryhodný certifikát SSL sloužící k připojení ke vzdálené pro službu IIS, můžete zrušit **Povolit nedůvěryhodný certifikát** zaškrtávací políčko. Informace o tom, jak přidat certifikát do nasazení webu zabezpečit, najdete v části **k zkontrolujte webové nasazení zabezpečené** dále v tomto článku.

1. Službu Web Deploy používat, musí tento mechanismus publikování Uživatelské jméno a heslo, které jste nastavili pro připojení k vzdálené plochy při prvním publikování balíčku.

   a. V **uživatelské jméno**, zadejte uživatelské jméno.

   b. V **heslo**, zadejte heslo.

   c. (Volitelné) Pokud chcete uložit toto heslo v tomto profilu, zvolte **uložit heslo**.

1. Chcete-li publikovat změny na vaši webovou roli, zvolte **publikovat**.

    Ve stavovém řádku zobrazí **publikovat spuštění**. Po dokončení publikování **publikování bylo úspěšné** se zobrazí. Změny jsou nyní nasazené webovou roli na virtuálním počítači. Nyní můžete spustit aplikaci Azure v prostředí Azure k testování změny.

### <a name="make-web-deploy-secure"></a>Zabezpečit webové nasazení

1. Nasazení webu používá certifikát podepsaný svým držitelem, nedůvěryhodné ve výchozím nastavení, která se nedoporučuje pro nahrávání citlivá data. Pokud potřebujete zabezpečit tento proces pro citlivá data, můžete přidat certifikát SSL, který se má použít pro nasazení webu připojení. Tento certifikát musí být důvěryhodný certifikát, který můžete získat od certifikační autority (CA).

    Zabezpečit nasazení webu pro každý virtuální počítač pro jednotlivé webové role, musíte nahrát důvěryhodný certifikát, který chcete použít pro web nasadit na portálu Azure. Tento certifikát zajišťuje, že je certifikát přidat k virtuálnímu počítači, který je vytvořen pro webovou roli při publikování aplikace.

1. Pokud chcete přidat do služba IIS používat pro vzdálená připojení důvěryhodná certifikát SSL, postupujte takto:

   a. Pro připojení k virtuálnímu počítači, na kterém běží role webové, vyberte instanci webové role v **Průzkumník cloudu** nebo **Průzkumníka serveru**a potom vyberte **připojit pomocí vzdálené plochy**  příkaz. Podrobné pokyny o tom, jak připojit k virtuálnímu počítači, najdete v části [povolit připojení ke vzdálené ploše pro roli ve službě Azure Cloud Services pomocí sady Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md). Prohlížeči výzva ke stažení `.rdp` souboru.

   b. Chcete-li přidat certifikát SSL, otevřete službu správy ve Správci služby IIS. Ve Správci služby IIS povolit protokol SSL otevřením **vazby** odkaz **akce** podokně. **Přidat vazbu webu** zobrazí se dialogové okno. Zvolte **přidat**a potom vyberte HTTPS v **typ** rozevíracího seznamu. V **certifikát SSL** vyberte certifikát SSL, aby měl podepsaný Certifikační autoritou a že jste nahráli na portál Azure. Další informace najdete v tématu [konfigurace nastavení připojení pro službu správy](http://go.microsoft.com/fwlink/?LinkId=215824).

      > [!NOTE]
      > Pokud chcete přidat důvěryhodný certifikát SSL, žlutý trojúhelník upozornění se již v **Průvodci publikováním**.

## <a name="include-files-in-the-service-package"></a>Vložené soubory v balíčku služby

Možná budete muset zahrnout konkrétní soubory do balíčku služby, aby byly k dispozici na virtuálním počítači, který se vytvoří pro roli. Můžete například chtít přidat `.exe` nebo `.msi` soubor, který je používán spouštěcí skript do vašeho balíčku služby. Nebo možná budete muset přidat sestavení, které vyžaduje projekt webové role nebo pracovní role. Vložené soubory, je nutné je přidat do řešení pro aplikaci Azure.

1. K přidání sestavení do balíčku služby, použijte následující kroky:

   a. V **Průzkumníku**, otevřete uzel projektu pro projekt, který chybí odkazované sestavení.
   b. Chcete-li do projektu přidejte sestavení, otevřete místní nabídku pro **odkazy** složku a potom vyberte **přidat odkaz na**. Otevře se dialogové okno Přidat odkaz.
   c. Vyberte odkaz, na který chcete přidat a pak zvolte **OK**. Odkaz je přidán do seznamu v části **odkazy** složky.
   d. Otevřete místní nabídku pro sestavení, které jste přidali a zvolte **vlastnosti**. **Vlastnosti** se zobrazí v okně.

      Zahrnout toto sestavení v balíčku služby, **kopie místního seznamu** zvolte **True**.
1. V **Průzkumníku řešení** otevřete uzel projektu pro projekt, který chybí odkazované sestavení.

1. Chcete-li do projektu přidejte sestavení, otevřete místní nabídku pro **odkazy** složku a potom vyberte **přidat odkaz na**. **Přidat odkaz na** otevře se dialogové okno.

1. Vyberte odkaz, na který chcete přidat a pak zvolte **OK** tlačítko.

    Odkaz je přidán do seznamu v části **odkazy** složky.

1. Otevřete místní nabídku pro sestavení, které jste přidali a zvolte **vlastnosti**. Zobrazí se okno Vlastnosti.

1. Zahrnout toto sestavení v balíčku služby, **místní kopie** vyberte **True**.

1. Chcete-li zahrnout soubory v balíčku služby, které byly přidány do projektu webové role, otevřete místní nabídky souboru a poté zvolte **vlastnosti**. Z **vlastnosti** okně zvolte **obsahu** z **akce sestavení** pole se seznamem.

1. Chcete-li zahrnout soubory v balíčku služby, které byly přidány do projektu role pracovního procesu, otevřete místní nabídky souboru a poté zvolte **vlastnosti**. Z **vlastnosti** okně zvolte **kopírovat, pokud je novější** z **kopírovat do výstupního adresáře** pole se seznamem.

## <a name="next-steps"></a>Další postup

Další informace o publikování do Azure ze sady Visual Studio najdete v tématu [Průvodci publikováním aplikace Azure](vs-azure-tools-publish-azure-application-wizard.md).
