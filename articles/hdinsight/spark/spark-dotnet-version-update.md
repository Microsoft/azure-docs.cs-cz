---
title: Aktualizace .NET pro Apache Spark na verzi v 1.0 v HDI
description: Přečtěte si o aktualizaci .NET pro Apache Spark verze na 1,0 v HDI a o tom, jak to ovlivní váš stávající kód a clustery.
author: Niharikadutta
ms.author: nidutta
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/05/2021
ms.openlocfilehash: a1602f29a6d0066ec3c99e990532411621652c47
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788125"
---
# <a name="updating-net-for-apache-spark-to-version-v10--in-hdinsight"></a>Aktualizace .NET pro Apache Spark na verzi v 1.0 v HDInsight

Tento dokument mluví s první hlavní verzí [rozhraní .NET pro Apache Spark](https://github.com/dotnet/spark)a může ovlivnit vaše aktuální produkční kanály v clusterech HDInsight.

## <a name="about-net-for-apache-spark-version-100"></a>O rozhraní .NET pro Apache Spark verze 1.0.0

Toto je první [hlavní oficiální verze](https://github.com/dotnet/spark/releases/tag/v1.0.0) rozhraní .net pro Apache Spark, která poskytuje úplnost rozhraní API dataframe pro Spark 2.4. x a také Spark 3.0. x spolu s dalšími funkcemi. Úplný seznam všech funkcí, vylepšení a oprav chyb najdete v [poznámkách k verzi oficiální verze v 1.0.0](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md).
Další důležitou věcí je, že tato verze není **kompatibilní s** předchozími verzemi `Microsoft.Spark` a `Microsoft.Spark.Worker` . Pokud plánujete upgradovat aplikaci .NET pro Apache Spark tak, aby byla kompatibilní s nástrojem v 1.0.0, přečtěte si [příručku k migraci](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) .

## <a name="using-net-for-apache-spark-v10-in-hdinsight"></a>Použití rozhraní .NET pro Apache Spark v 1.0 v HDInsight

I když aktuální clustery HDI nebudou ovlivněny (to znamená, že budou mít stále stejnou verzi jako dříve), nově vytvořené clustery HDI budou mít k dispozici nejnovější verzi v 1.0.0 .NET pro Apache Spark. Co to znamená, když:

- **Máte starší cluster HDI**: Pokud chcete upgradovat aplikaci Spark .NET na verzi v 1.0.0 (doporučeno), budete muset aktualizovat `Microsoft.Spark.Worker` verzi v clusteru HDI. Další informace naleznete v [části měnící se verze rozhraní .NET for Apache Spark v clusteru HDI](#changing-net-for-apache-spark-version-on-hdinsight).
Pokud nechcete aktualizovat aktuální verzi rozhraní .NET pro Apache Spark v aplikaci, není nutné provádět žádné další kroky.  

- **Máte nový cluster HDI**: Pokud chcete upgradovat aplikaci Spark .NET na verzi v 1.0.0 (doporučeno), nemusíte provádět žádné kroky ke změně pracovního procesu v HDI, ale budete muset použít odkaz na [Průvodce migrací](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) , abyste pochopili kroky potřebné k aktualizaci kódu a kanálů.
Pokud nechcete měnit aktuální verzi rozhraní .NET pro Apache Spark v aplikaci, bude nutné změnit verzi v clusteru HDI z verze 1.0 (výchozí v nových clusterech) na libovolnou verzi, kterou používáte. Další informace naleznete v [části měnící se verze rozhraní .NET for Apache Spark v clusteru HDI](spark-dotnet-version-update.md#changing-net-for-apache-spark-version-on-hdinsight).  

## <a name="changing-net-for-apache-spark-version-on-hdinsight"></a>Změna rozhraní .NET pro Apache Spark verze v HDInsight

### <a name="deploy-microsoftsparkworker"></a>Nasazení Microsoft. spark. Worker

`Microsoft.Spark.Worker` je komponenta back-end, která je umístěná na jednotlivých pracovních uzlech clusteru Spark. Pokud chcete spustit jazyk C# UDF (uživatelsky definovaná funkce), musí Spark pochopit, jak spustit modul CLR .NET pro spuštění tohoto systému souborů UDF. `Microsoft.Spark.Worker` poskytuje kolekci tříd pro Spark, které tuto funkci povolují. V závislosti na verzi rozhraní .NET pro Apache Spark, kterou chcete nasadit do clusteru HDI, vyberte verzi pracovního procesu.

1. Stáhněte si verzi Microsoft. spark. Worker pro Linux konkrétní verze. Pokud třeba chcete `.NET for Apache Spark v1.0.0` , Stáhněte si soubor [Microsoft. spark. work. netcoreapp 3.1. Linux-x64-1.0.0. tar. gz](https://github.com/dotnet/spark/releases/tag/v1.0.0).  

2. Stáhněte si skript [install-Worker.sh](https://github.com/dotnet/spark/blob/master/deployment/install-worker.sh) pro instalaci spustitelných souborů pracovních procesů stažených v kroku 1 do všech pracovních uzlů vašeho clusteru HDI.  

3. Výše uvedené soubory nahrajte na účet Azure Storage, ke kterému má váš cluster přístup. Další podrobnosti najdete [v článku věnovaném nasazení rozhraní .NET pro Apache Spark HDI](/dotnet/spark/tutorials/hdinsight-deployment#upload-files-to-azure) .

4. Spusťte `install-worker.sh` skript na všech pracovních uzlech clusteru pomocí akcí skriptů. Další informace najdete [v článku věnovaném nasazení rozhraní .NET pro Apache Spark HDI](/dotnet/spark/tutorials/hdinsight-deployment#run-the-hdinsight-script-action) .

### <a name="update-your-application-to-use-specific-version"></a>Aktualizujte aplikaci tak, aby používala konkrétní verzi.

Aplikaci .NET for Apache Spark můžete aktualizovat tak, aby používala konkrétní verzi. v projektu vyberte požadovanou verzi [balíčku NuGet Microsoft. Spark](https://www.nuget.org/packages/Microsoft.Spark/) . Nezapomeňte si prohlédnout poznámky k verzi konkrétní verze a [Průvodce migrací](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) , jak je uvedeno výše, pokud zvolíte aktualizaci aplikace na verzi v 1.0.0.

## <a name="faqs"></a>Nejčastější dotazy

### <a name="will-my-existing-hdi-cluster-with-version--100-start-failing-with-the-new-release"></a>Bude můj stávající cluster HDI s verzí < 1.0.0, že se v nové verzi nedaří začít?

Stávající clustery HDI budou mít i nadále stejnou předchozí verzi pro .NET pro Apache Spark a stávající aplikace (s předchozí verzí Spark .NET) to nebude mít vliv.

## <a name="next-steps"></a>Další kroky

[Nasazení aplikace .NET pro Apache Spark v HDInsight](/dotnet/spark/tutorials/hdinsight-deployment)