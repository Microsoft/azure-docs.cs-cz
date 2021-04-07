---
title: Bezpečně spravovat závislosti jar – Azure HDInsight
description: Tento článek popisuje osvědčené postupy pro správu závislostí archivu Java (JAR) pro aplikace HDInsight.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/05/2020
ms.openlocfilehash: 9868e32665c89bbe9aadc06f1c2834704e6534e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942379"
---
# <a name="safely-manage-jar-dependencies"></a>Zabezpečená správa závislostí Jar

Komponenty nainstalované v clusterech HDInsight mají závislosti na knihovnách třetích stran. V rámci těchto integrovaných komponent jsou obvykle odkazovány konkrétní verze běžných modulů, jako je guava. Když odešlete aplikaci s jejími závislostmi, může dojít ke konfliktu mezi různými verzemi stejného modulu. Pokud verze komponenty, na kterou odkazujete v rámci třídy CLASSPATH, mohou předdefinované komponenty vyvolat výjimky z důvodu nekompatibility verzí. Pokud však integrované komponenty vkládají své závislosti na cestu k cestě, může vaše aplikace vyvolat chyby jako `NoSuchMethod` .

Chcete-li se vyhnout konfliktu verzí, zvažte vystínování závislostí aplikace.

## <a name="what-does-package-shading-mean"></a>Co znamená stínování balíčku?
Stínování nabízí způsob, jak zahrnout a přejmenovat závislosti. Přemístí třídy a přepíše ovlivněné bytové prostředí a prostředky, aby vytvořil soukromou kopii vašich závislostí.

## <a name="how-to-shade-a-package"></a>Jak vystínovat balíček?

### <a name="use-uber-jar"></a>Použít Uber – jar
Uber-JAR je jeden soubor JAR, který obsahuje jar aplikace i jeho závislosti. Závislosti v Uber-jar nejsou ve výchozím nastavení stínové. V některých případech to může způsobit konflikt verze, pokud jiné součásti nebo aplikace odkazují na jinou verzi těchto knihoven. Pokud se tomu chcete vyhnout, můžete vytvořit soubor Uber-Jar s některými (nebo všemi) závislostmi, které jsou ve stínovém formátu.

### <a name="shade-package-using-maven"></a>Stínovat balíček pomocí Maven
Maven může sestavovat aplikace napsané v jazyce Java i v Scala. Maven-odstín – modul plug-in vám umožní snadno vytvořit stínovaný Uber.

Následující příklad ukazuje soubor `pom.xml` , který se aktualizoval pro barevný nádech balíčku pomocí Maven-stínového modulu plug-in.  Oddíl XML `<relocation>…</relocation>` přesune třídy z balíčku `com.google.guava` do balíčku `com.google.shaded.guava` přesunutím odpovídajících položek souboru jar a přepsáním ovlivněného bajtu.

Po změně `pom.xml` můžete provést příkaz `mvn package` pro sestavení vybarveného uberového jar.

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
SBT je také nástroj sestavení pro Scala a Java. SBT nemá modul plug-in pro stín, jako je Maven-stínový modul plug-in. Můžete upravit `build.sbt` soubor pro barevný nádech balíčků. 

Například pro barevný stín `com.google.guava` můžete do souboru přidat následující příkaz `build.sbt` :

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Pak můžete spustit `sbt clean` a a `sbt assembly` sestavit stínovaný soubor JAR. 

## <a name="next-steps"></a>Další kroky

* [Použití nástrojů HDInsight IntelliJ](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

* [Vytvoření aplikace Scala Maven pro Spark v IntelliJ](./apache-spark-create-standalone-application.md)