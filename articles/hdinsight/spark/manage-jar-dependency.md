---
title: Správa závislostí JAR – Azure HDInsight
description: Tento článek popisuje osvědčené postupy pro správu závislostí archivu Java (JAR) pro aplikace HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: da3387dd9846847f7643ded43c8cbff8ed8b166e
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77135730"
---
# <a name="jar-dependency-management-best-practices"></a>Osvědčené postupy pro správu závislostí JAR

Komponenty nainstalované v clusterech HDInsight mají závislosti na knihovnách třetích stran. V rámci těchto integrovaných komponent jsou obvykle odkazovány konkrétní verze běžných modulů, jako je guava. Když odešlete aplikaci s jejími závislostmi, může dojít ke konfliktu mezi různými verzemi stejného modulu. Pokud verze komponenty, na kterou odkazujete v rámci třídy CLASSPATH, mohou předdefinované komponenty vyvolat výjimky z důvodu nekompatibility verzí. Pokud však předdefinované komponenty vloží své závislosti do cesty k cestě, může vaše aplikace vyvolat chyby, jako `NoSuchMethod`.

Chcete-li se vyhnout konfliktu verzí, zvažte vystínování závislostí aplikace.

## <a name="what-does-package-shading-mean"></a>Co znamená stínování balíčku?
Stínování nabízí způsob, jak zahrnout a přejmenovat závislosti. Přemístí třídy a přepíše ovlivněné bytové prostředí a prostředky, aby vytvořil soukromou kopii vašich závislostí.

## <a name="how-to-shade-a-package"></a>Jak vystínovat balíček?

### <a name="use-uber-jar"></a>Použít Uber – jar
Uber-JAR je jeden soubor JAR, který obsahuje jar aplikace i jeho závislosti. Závislosti v Uber-jar nejsou ve výchozím nastavení stínové. V některých případech to může způsobit konflikt verze, pokud jiné součásti nebo aplikace odkazují na jinou verzi těchto knihoven. Pokud tomu chcete předejít, můžete vytvořit Uber soubor s některými (nebo všemi) závislostmi, které jsou ve stínovém formátu.

### <a name="shade-package-using-maven"></a>Stínovat balíček pomocí Maven
Maven může sestavovat aplikace napsané v jazyce Java i v Scala. Maven-odstín – modul plug-in vám umožní snadno vytvořit stínovaný Uber.

Následující příklad ukazuje soubor `pom.xml`, který se aktualizoval pro barevný nádech balíčku pomocí Maven-stínového modulu plug-in.  Oddíl XML `<relocation>…</relocation>` přesouvá třídy z balíčku `com.google.guava` do balíčku `com.google.shaded.guava` přesunutím odpovídajících položek souborů JAR a přepsáním ovlivněného bajtového kódu.

Po změně `pom.xml`můžete spustit `mvn package` a vytvořit tak stínový uberový jar.

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

### <a name="shade-package-using-sbt"></a>Stínovat balíček pomocí SBT
SBT je také nástroj sestavení pro Scala a Java. SBT nemá modul plug-in pro stín, jako je Maven-stínový modul plug-in. Můžete upravit soubor `build.sbt` pro barevný nádech balíčků. 

Chcete-li například stínovat `com.google.guava`, můžete do souboru `build.sbt` přidat následující příkaz:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Potom můžete spustit `sbt clean` a `sbt assembly` vytvořit stínovaný soubor JAR. 

## <a name="next-steps"></a>Další kroky

* [Použití nástrojů HDInsight IntelliJ](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [Vytvoření aplikace Scala Maven pro Spark v IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
