---
title: Poznámky k verzi sady Azure SDK pro .NET 2.5.1
description: Poznámky k verzi sady Azure SDK pro .NET 2.5.1
services: app-service
documentationcenter: .net,nodejs,java
author: Juliako
manager: erikre
editor: ''
ms.assetid: 8d3d815f-bb58-447e-8ff0-f9b9603c7b00
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/10/2016
ms.author: juliako
ms.openlocfilehash: 0a422b02623a18ac6a1eef460bbada681672e69f
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2017
ms.locfileid: "23957411"
---
# <a name="azure-sdk-for-net-251-release-notes"></a>Poznámky k verzi sady Azure SDK pro .NET 2.5.1
Tento dokument obsahuje poznámky k verzi sady Azure SDK pro .NET 2.5.1 verzi. 

## <a name="azure-sdk-for-net-251-release-notes"></a>Poznámky k verzi sady Azure SDK pro .NET 2.5.1
Následující části jsou nové funkce a aktualizace v sadě Azure SDK pro .NET 2.5.1.

* Nové features\scenarios související s **Web Tools Extensions**. 
  
  * Weby Azure byla přejmenována na Azure App Service. 
  * Byla přidána podpora Azure API Apps (Preview), aby zákazníci publikovat projekty ASP.NET jako aplikace API a pak použít přidáním > gesto klienta aplikace API Azure v jazyce C# projekty pro generování kódu na základě struktury nasazené aplikace API. 
  * Uzel weby v Průzkumníku serveru se už nepoužívá místo uzlu Azure App Service, která obsahuje podporu pro prostředek skupiny založené seskupení aplikace Azure API, mobilní aplikace a webové aplikace.
  * Byla přidána podpora Azure Mobile Apps (Preview) tak, aby zákazníci vytvářet nové projekty Mobile Apps, přidejte řadiče Mobile Apps, publikování projektů a vzdáleně ladit aplikace.
  * Přidat > gesto klienta aplikace API Azure nyní podporuje místní soubory JSON pro Swagger, takže webového rozhraní API mohou vývojáři NuGets třetích stran, jako je Swashbuckle generovat Swagger nebo ho ručně vytvořit. Tímto způsobem klienta mohou vývojáři funkce generování kódu využívat všechny koncového bodu Swaggeru v projektů C#. 
  * Webová aplikace a aplikace API, které vylepšily publikování dialogová okna pro podporu Azure portálu koncept seskupení prostředků a výběr nebo vytvoření skupiny prostředků Azure a plány služby App Service jsou reprezentované v nové webové aplikace a aplikace API zřizování dialogu. 
  * Uzlů Azure Průzkumníka serveru aplikace API poskytují odkazy na přímý odkaz na aplikace API v portálu Azure a také další funkce, například vysílání datového proudu protokolu a vzdálené ladění.
    
    Známé problémy a aktuální omezení v Azure SDK .NET 2.5.1 [to](app-service-release-notes.md#known_issues_2_5_1) části níže.
* Nové features\scenarios související s **nástroje HDInsight** v sadě Visual Studio, které jsou povolené v této verzi. 
  
  * Místní ověření hive skriptů. Klikněte na tlačítko Ověřit skriptu na panelu nástrojů. Pokud jsou k dispozici žádné chyby ve vašem skriptu. 
  * Vylepšení ladění úloh Hive. Nyní můžete ladit úlohy Hive díky přístupu k protokoly Yarn v sadě Visual Studio. Pokud vaše aplikace má problémy s výkonem, zkoumání protokoly YARN poskytují užitečné informace...
  * (Verze public Preview) Automatické dokončování – klíčové slovo a IntelliSense podpora Hive. Můžete vytvářet skripty Hive, nástroje HDInsight pro Visual Studio přidat podporu automatické dokončování – klíčové slovo a IntelliSense pro Hive.
  * Podpora Storm. Nyní můžete nástroje HDInsight pro Visual Studio pro vývoj Storm topologie/funkcích Spouts nebo funkce Bolts v jazyce C#. Můžete odeslat vyvinuté topologie ke clusteru Storm a zobrazit stav topologie/bolt/spout. Chcete-li vyřešit vaše Storm topologie nebo funkce Bolts nebo funkcích Spouts můžete zákazníka protokoly a protokoly systému. Můžete také použít existující prostředky JAVA v Storm v HDInsight.
    
    Další informace najdete v tématu [Začínáme pomocí nástrojů Hadoop HDInsight pro Visual Studio](../hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a id="known_issues_2_5_1"></a>Azure SDK pro .NET 2.5.1 – známé problémy a omezení
* Aplikace API Azure se zobrazí jako cíl nasazení pro mobilní aplikace. Webové aplikace musí být pouze cíl pro Mobile Apps až další verze. 
* Azure zřizování aplikace API může mít za následek úspěch ale občas selhat se aktualizovat průběh v okně aktivita služby Azure App Service. Řešením je zkontrolovat stav nové aplikace API Azure na portálu Azure. 
* Soubor > Nový Projekt > aplikace API > F5 prostředí výsledkem chyby protokolu HTTP, protože neexistuje žádný default/index.html. Alternativní řešení je ručně přejděte na adresu URL /api/values. 
* Ikony v Průzkumníku serveru zobrazí přerušovaně plochou. Restartování VS řeší tento problém. 
* Pokud je vyvolána výjimka při webovou aplikaci nebo aplikaci API zřizování (například chyb překročil kvótu nebo duplicitní název aplikace API Azure brány), zobrazit chyby některé nezpracovaný text JSON. 
* Při kontrole Application Insights v okamžiku vytvoření projektu přerušované vytváření projektu problémy.
* V některých případech generovaný kód klienta aplikace Azure API chybí obory názvů, potřebují ručně zahrnutých (nebo automaticky importovány pomocí sady Visual Studio upozornění) pro kód mohl zkompilovat. 
* Projekty pro mobilní aplikace by měla být publikována do webové aplikace, ale je třeba vybrat, kterou jste vytvořili jako mobilní aplikace na portálu Azure, protože projekty mobilní aplikace vyžadují databázi lokality. 
* Úvodní stránky pro Mobile Apps používá termín "Mobilní služba" místo "mobilní aplikace" 
* Vytvoření projektu mobilní aplikace může trvat až několik minut pro vytvoření. 
* Během aplikace API zřizování (v některých případech) chybu zpátky ze odrážející rozhraní API služby Azure, aby oprávnění nelze nastavit správně, zatímco aplikace API správně zřízený a je připravený k použití. Můžete ručně nastavit oprávnění pomocí portálu Azure.
* Application Insights nepodporuje šablony aplikace API a mobilní aplikace.
* Projekty aplikace API nelze použít ve spojení s projekty cloudových služeb.
* Šablony projektu aplikace API jsou dostupné jenom v C#.
* Aplikace API spotřeba využít kontextovou nabídku "Přidat klienta aplikace Azure API" je podporována pouze v jazyce C#.

