---
title: Správa závislostí JAR – Azure HDInsight
description: Tento článek popisuje osvědčené postupy pro správu závislostí Java Archive (JAR) pro aplikace HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: da3387dd9846847f7643ded43c8cbff8ed8b166e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77135730"
---
# <a name="jar-dependency-management-best-practices"></a>Osvědčené postupy správy závislostí JAR

Součásti nainstalované v clusterech HDInsight mají závislosti na knihovnách třetích stran. Obvykle je specifická verze běžných modulů, jako je Guava odkazuje na tyto vestavěné komponenty. Při odeslání aplikace s jeho závislosti, může způsobit konflikt mezi různými verzemi stejného modulu. Pokud verze komponenty, na kterou odkazujete v cestě pro třídy jako první, předdefinované součásti mohou vyvolat výjimky z důvodu nekompatibility verze. Pokud však integrované součásti nejprve vloží své závislosti do cesty pro `NoSuchMethod`třídy, může aplikace vyvolat chyby jako .

Chcete-li se vyhnout konfliktu verzí, zvažte stínování závislostí aplikací.

## <a name="what-does-package-shading-mean"></a>Co znamená stínování balíčků?
Stínování poskytuje způsob, jak zahrnout a přejmenovat závislosti. Přemístí třídy a přepíše ovlivněný bytecode a prostředky k vytvoření soukromé kopie vašich závislostí.

## <a name="how-to-shade-a-package"></a>Jak zastínit balíček?

### <a name="use-uber-jar"></a>Použijte uber-jar
Uber-jar je jeden jar soubor, který obsahuje jak aplikaci jar a jeho závislosti. Závislosti v Uber-jar nejsou ve výchozím nastavení stínované. V některých případech to může způsobit konflikt verze, pokud jiné součásti nebo aplikace odkazují na jinou verzi těchto knihoven. Chcete-li tomu zabránit, můžete vytvořit soubor Uber-Jar s některými (nebo všemi) závislostmi ve stínovaných.

### <a name="shade-package-using-maven"></a>Shade balíček pomocí Maven
Maven může vytvářet aplikace napsané jak v Javě, tak v Scale. Maven-shade-plugin vám pomůže snadno vytvořit stínovaný uber-jar.

Následující příklad ukazuje `pom.xml` soubor, který byl aktualizován na stín balíček pomocí maven-shade-plugin.  Oddíl `<relocation>…</relocation>` XML přesune třídy z balíčku `com.google.guava` do balíčku `com.google.shaded.guava` přesunutím odpovídajících položek souboru JAR a přepsáním ovlivněného bytekódu.

Po `pom.xml`změně můžete `mvn package` provést sestavení stínované uber-jar.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>Stínovací balíček pomocí SBT
SBT je také nástroj pro sestavení Scala a Java. SBT nemá odstín plugin jako maven-shade-plugin. Soubor můžete `build.sbt` upravit tak, aby stínily balíčky. 

Chcete-li například zastínit `com.google.guava`, můžete `build.sbt` do souboru přidat příkaz níže:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Pak můžete `sbt clean` spustit `sbt assembly` a vytvořit stínovaný jar soubor. 

## <a name="next-steps"></a>Další kroky

* [Použití nástrojů INtelliJ HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [Vytvoření aplikace Scala Maven pro Spark v IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
