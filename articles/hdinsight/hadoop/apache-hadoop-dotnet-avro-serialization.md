---
title: Serializaci dat ve službě Azure - Microsoft Avro Library - Hadoop | Microsoft Docs
description: Zjistěte, jak k serializaci a deserializaci dat v Hadoop v HDInsight pomocí Microsoft Avro Library k uchovávání paměť, databáze nebo souboru.
keywords: avro hadoop avro
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c78dc20d-5d8d-4366-94ac-abbe89aaac58
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.custom: hdiseo17may2017
ms.openlocfilehash: 0d195ab3b84a522eae4010f3b08a829f7056a35f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202337"
---
# <a name="serialize-data-in-hadoop-with-the-microsoft-avro-library"></a>Serializovat data v Hadoop pomocí Microsoft Avro Library

>[!NOTE]
>Avro SDK je podporován společností Microsoft. Knihovna je podporován komunity s otevřeným zdrojem. Zdroje pro knihovnu jsou umístěné v [Githubu](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

Toto téma ukazuje, jak používat [Microsoft Avro Library](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro) k serializaci objektů a jiné datové struktury do datových proudů, aby je uchoval do paměti, databáze nebo souboru. Také ukazuje, jak k deserializaci je obnovit původní objekty.

[!INCLUDE [windows-only](../../../includes/hdinsight-windows-only.md)]

## <a name="apache-avro"></a>Apache Avro
<a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro Library</a> implementuje Apache Avro systému serializace dat pro prostředí rozhraní Microsoft.NET. Apache Avro poskytuje formát pro výměnu kompaktní binární data pro serializaci. Používá <a href="http://www.json.org" target="_blank">JSON</a> k definování jazykově nezávislého schématu, které poskytuje vzájemnou vzájemná funkční spolupráce jazyka. Data serializovaná v jednom jazyce lze číst v jiném. Aktuálně jsou podporované C, C++, C#, Java, PHP, Python a Ruby. Podrobné informace o formátu lze nalézt v <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">Apache Avro Specification</a>. 

>[!NOTE]
>Microsoft Avro Library nepodporuje vzdálené procedury volání (RPC) součástí téhle specifikaci.
>

Serializovaná reprezentace objektu v systému Avro se skládá ze dvou částí: schéma a skutečnou hodnotu. Schématu Avro popisuje model nezávislé na jazyku data serializovaná data s JSON. Zobrazí se node souběžně s binární reprezentace data. Každý objekt k zapsání pomocí žádné režijní náklady na hodnotu, provedení serializace rychlé a reprezentaci malé s odděleně od binární reprezentace schématu umožňuje.

## <a name="the-hadoop-scenario"></a>Scénář Hadoop
V Azure HDInsight a dalších prostředích s Apache Hadoop se často používá formát serializace Apache Avro. Avro nabízí pohodlný způsob, jak představují komplexní datové struktury v rámci úlohy Hadoop MapReduce. Formát Avro soubory (soubor kontejneru objektu Avro) má byly navrženy pro podporu Distribuovaný programovací model MapReduce. Klíčové funkce, která umožní, že distribuce je, že soubory jsou "rozdělitelné" v tom smyslu, že jeden vyhledat libovolný bod v souboru a začít číst od určitého bloku.

## <a name="serialization-in-avro-library"></a>Serializace v knihovně Avro
Knihovny .NET pro Avro podporuje dva způsoby serializaci objektů:

* **reflexe** – schéma JSON pro typy automaticky vychází z dat kontrakt atributy typy .NET, které mají být serializován.
* **Obecné záznam** -schématu A JSON je explicitně určena v záznamu reprezentována [ **AvroRecord** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) třídy, pokud žádné typy .NET existuje popisují schéma pro data, která mají být serializován.

Pokud schéma dat je znám zapisovače i čtečku datového proudu, data lze odeslat bez jeho schématu. V případech, pokud se používá soubor Avro objektu kontejneru, schéma je uloženo v souboru. Další parametry, například kodek použitý pro kompresi dat lze zadat. Tyto scénáře jsou popsané v další podrobnosti a ukazuje následující příklady kódu:

## <a name="install-avro-library"></a>Nainstaluje Avro knihovny
Je potřeba splnit následující před instalací knihovny:

* <a href="http://www.microsoft.com/download/details.aspx?id=17851" target="_blank">Rozhraní Microsoft .NET Framework 4</a>
* <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (6.0.4 nebo novější)

Všimněte si, že závislost Newtonsoft.Json.dll automaticky stažena v instalaci Microsoft Avro Library. Postup je uvedené v následující části:

Microsoft Avro Library je distribuován jako balíčku NuGet, který lze nainstalovat ze sady Visual Studio pomocí následujícího postupu:

1. Vyberte **projektu** -> karta **spravovat balíčky NuGet...**
2. Vyhledejte "Microsoft.Hadoop.Avro" v **Online hledání** pole.
3. Klikněte **nainstalovat** vedle položky **knihovnu Microsoft Azure HDInsight Avro**.

Všimněte si, že Newtonsoft.Json.dll (> = 6.0.4) závislostí je také automaticky staženy s Microsoft Avro Library.

Microsoft Avro Library zdrojový kód je k dispozici na [Githubu](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

## <a name="compile-schemas-using-avro-library"></a>Kompilace pomocí knihovny Avro schémata
Microsoft Avro Library obsahuje nástroj pro generování kódu, který umožňuje vytváření typy C# automaticky v závislosti na dříve definované schéma JSON. Nástroj pro generování kódu není distribuován jako binární spustitelný soubor, ale je možné snadno sestavit pomocí následujícího postupu:

1. Stáhněte si soubor .zip na nejnovější verzi sady SDK HDInsight zdrojového kódu z <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">Microsoft .NET SDK pro Hadoop</a>. (Klikněte na tlačítko **Stáhnout** ikonu, není **stáhne** karta.)
2. Extrahujte sady SDK HDInsight do adresáře na počítači s rozhraní .NET Framework 4 nainstalovaný a připojený k Internetu pro stahování balíčků NuGet potřebné závislosti. Níže předpokládáme, že zdrojový kód je extrahovány do C:\SDK.
3. Přejděte do složky C:\SDK\src\Microsoft.Hadoop.Avro.Tools a spusťte build.bat. (Soubor volá MSBuild z distribučního 32bitová verze rozhraní .NET Framework. Pokud chcete použít 64bitová verze, upravte build.bat, následující komentáře v souboru.) Ujistěte se, že sestavení byla úspěšně dokončena. (Na některých systémů MSBuild může vytvořit upozornění. Tato upozornění nemají vliv na nástroj, dokud nedojde k žádným chybám sestavení.)
4. Kompilované nástroj se nachází v C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools.

Abyste se seznámili s syntaxe příkazového řádku, spusťte následující příkaz ze složky, kde se nachází nástroj pro generování kódu: `Microsoft.Hadoop.Avro.Tools help /c:codegen`

K testování nástroj, můžete vygenerovat třídy jazyka C# ze souboru schématu JSON ukázka součástí zdrojového kódu. Spusťte následující příkaz:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

To by měl vytvořit dvě C# soubory v aktuálním adresáři: SensorData.cs a Location.cs.

Abyste pochopili logiky, která používá nástroj pro generování kódu při převodu schéma JSON pro typy C#, naleznete v souboru GenerationVerification.feature umístěný v C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc.

Obory názvů se extrahují z schématu JSON pomocí logiku popsané v souboru uveden v předchozím odstavci. Obory názvů extrahována ze schématu mají přednost před ať se poskytuje s parametrem/n v příkazovém řádku nástroj. Pokud chcete přepsat obory názvů obsažené ve schématu, použijte parametr /nf. Například pokud chcete změnit všechny obory názvů z SampleJSONSchema.avsc my.own.nspace, spusťte následující příkaz:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="about-the-samples"></a>O ukázky
Šest příklady uvedené v tomto tématu ilustrují Microsoft Avro Library podporuje různé scénáře. Microsoft Avro Library je navržen pro práci s žádné datového proudu. V těchto příkladech je upravit data prostřednictvím datových proudů paměti místo souborů datových proudů nebo databází kvůli jednoduchosti a přehlednosti. Přístup použitý v produkčním prostředí závisí na požadavky na přesný scénář, zdroj dat a svazek, omezení výkonu a dalších faktorů.

První dva příklady ukazují, jak k serializaci a deserializaci dat do vyrovnávací paměti datového proudu pomocí reflexe a obecné záznamy. Schéma v obou případech se předpokládá, že se dělí čtení a zápis out-of-band.

Třetí a čtvrtý příklady ukazují, jak k serializaci a deserializaci dat pomocí souborů Avro objektu kontejneru. Pokud data uložená v souboru Avro kontejneru, jeho schématu je vždy uložit s ním, protože schéma musí být sdílená pro deserializaci.

Vzorku, který obsahuje první čtyři příklady si můžete stáhnout z <a href="http://code.msdn.microsoft.com/Serialize-data-with-the-86055923" target="_blank">ukázky kódu Azure</a> lokality.

Páté příklad ukazuje, jak používat vlastní kompresní kodek pro soubory kontejneru objektů Avro. Ukázka obsahující kód pro tento příklad si můžete stáhnout z <a href="http://code.msdn.microsoft.com/Serialize-data-with-the-67159111" target="_blank">ukázky kódu Azure</a> lokality.

Šesté příklad ukazuje použití Avro serializace k odesílání dat do úložiště objektů Blob v Azure a analyzujte ji pomocí clusteru služby HDInsight (Hadoop) Hive. Lze ji stáhnout z <a href="https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3" target="_blank">ukázky kódu Azure</a> lokality.

Tady jsou odkazy na šesti ukázky popsané v tématu:

* <a href="#Scenario1">**Serializace pomocí reflexe** </a> – schéma JSON pro typy k serializaci automaticky vychází z dat kontrakt atributy.
* <a href="#Scenario2">**Serializace s obecné záznam** </a> -schématu JSON je explicitně určena v záznamu, když je k dispozici pro reflexi žádný typ rozhraní .NET.
* <a href="#Scenario3">**Serializace objektu kontejneru soubory pomocí reflexe** </a> -schématu JSON je automaticky vytvořen a sdílet společně s serializovaná data prostřednictvím soubor Avro objektu kontejneru.
* <a href="#Scenario4">**Serializace objektu kontejneru soubory pomocí obecné záznam** </a> -schématu JSON jsou explicitně zadaný před serializaci a sdílet společně s dat přes soubor Avro objektu kontejneru.
* <a href="#Scenario5">**Serializace objektu kontejneru soubory pomocí vlastní kompresní kodek** </a> – příklad ukazuje, jak vytvořit soubor Avro objektu kontejneru s vlastní .NET provádění kodeků komprese dat Deflate.
* <a href="#Scenario6">**Nahrání dat do služby Microsoft Azure HDInsight pomocí Avro** </a> – příklad ukazuje, jak Avro serializace komunikuje se službou HDInsight. Aktivní předplatné Azure a přístup ke clusteru Azure HDInsight jsou nutné ke spuštění v tomto příkladu.

## <a name="Scenario1"></a>Příklad 1: Serializace pomocí reflexe
Schéma JSON pro typy můžete být automaticky vytvořené Microsoft Library Avro prostřednictvím reflexe z dat kontrakt atributy objektů jazyka C# k serializaci. Vytvoří Microsoft Avro Library [ **IAvroSeralizer<T>**  ](http://msdn.microsoft.com/library/dn627341.aspx) k identifikaci pole k serializaci.

V tomto příkladu objekty ( **SensorData** se členem **umístění** struktura) jsou serializovat na datový proud paměti a tento datový proud je zase deserializovat. Výsledkem je pak porovnána s počáteční instance potvrdit, že **SensorData** je stejný jako původní objekt obnovit.

Schéma v tomto příkladu se předpokládá, že sdílet mezi čtení a zápis, tak formát Avro objektu kontejneru se nevyžaduje. Příklad toho, jak k serializaci a deserializaci dat do vyrovnávací paměti pomocí reflexe ve formátu kontejneru objektu, pokud schéma musí být sdílená s daty v tématu <a href="#Scenario3">serializace objektu kontejneru soubory pomocí reflexe</a>.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using reflection.
            //No explicit schema definition is required - schema of serialized objects is automatically built.
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a memory stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set by using sample class and struct
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-2-serialization-with-a-generic-record"></a>Příklad 2: Serializace se záznamem obecné
Při reflexe nelze použít, protože data nelze reprezentovat prostřednictvím rozhraní .NET třídy s kontraktu dat, lze v záznamu obecné explicitně zadat schématu JSON. Tato metoda je nižší než pomocí reflexe. V takových případech schéma pro data mohou být také dynamické, tedy není známý v době kompilace. Data reprezentován jako soubory hodnot oddělených čárkami (CSV), jejichž schématu neznámý, dokud se převede do formátu Avro v době běhu je příklad toto řazení dynamické scénáře.

Tento příklad ukazuje, jak vytvořit a používat [ **AvroRecord** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) k explicitnímu zadání schématu JSON, postup naplnění s daty a pak k serializaci a deserializaci. Výsledkem je pak porovnána s počáteční instance potvrďte, že záznam obnovit je stejný jako původní.

Schéma v tomto příkladu se předpokládá, že sdílet mezi čtení a zápis, tak formát Avro objektu kontejneru se nevyžaduje. Příklad toho, jak k serializaci a deserializaci dat do vyrovnávací paměti pomocí obecné záznam ve formátu kontejneru objektu při schématu musí být součástí serializovaná data, naleznete v části <a href="#Scenario4">serializace objektu kontejneru soubory pomocí obecné záznam</a> příklad.

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    using Microsoft.Hadoop.Avro;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set by using a generic record.
        //A generic record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of the generic record,
        //which in turn is then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a memory stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using generic record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>Ukázka 3: Serializace soubory kontejneru objektů a serializace pomocí reflexe
Je podobná scénář v tomto příkladu <a href="#Scenario1"> prvním příkladu</a>, tam, kde je schéma implicitně zadán pomocí reflexe. Rozdíl je, že tady, schéma se předpokládá, že být známé čtečce deserializuje ho. **SensorData** objekty k serializaci a jejich implicitně určené schéma uložené v souboru kontejneru objektu Avro reprezentována [ **AvroContainer** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) třídy.

Je v tomto příkladu se serializovat data [ **SequentialWriter<SensorData>**  ](http://msdn.microsoft.com/library/dn627340.aspx) a deserializovat s [ **SequentialReader<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx). Výsledkem je pak porovnána s počáteční instancí zajistit identity.

Data do souboru objektu kontejneru se komprimují prostřednictvím výchozí [ **Deflate** ] [ deflate-100] kompresní kodek z rozhraní .NET Framework 4. Najdete v článku <a href="#Scenario5"> páté příklad</a> v tomto tématu se dozvíte, jak používat novější a vyšší verze [ **Deflate** ] [ deflate-110] komprese kodeků, které jsou k dispozici v rozhraní .NET Framework 4.5.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes the sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the Deflate codec.
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data is compressed using the Deflate codec.
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>Ukázka 4: Serializace pomocí obecné záznam soubory kontejneru objektů a serializace
Je podobná scénář v tomto příkladu <a href="#Scenario2"> druhém příkladu</a>, kde je explicitně zadat schématu s JSON. Rozdíl je, že tady, schéma se předpokládá, že být známé čtečce deserializuje ho.

Sada testovacích dat. se shromažďují do seznamu [ **AvroRecord** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) objektů prostřednictvím explicitně definované schéma JSON a pak uloženy v souboru kontejneru objektu reprezentována [ **AvroContainer** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) třídy. Tento soubor kontejner vytvoří zapisovač, který se používá k serializaci dat, nekomprimovaným paměti datový proud, který je pak uloží do souboru. [ **Codec.Null** ](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) parametr použitý k vytvoření čtečky Určuje, že není komprimována tato data.

Data se pak čtení ze souboru a deserializovat do kolekce objektů. Tato kolekce je ve srovnání s počáteční seznam záznamů Avro potvrďte, že jsou identické.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;
        using Microsoft.Hadoop.Avro;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes a sample data set by using a generic record and Avro object container files.
            //Serialized data is not compressed.
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file.
                //Create a MemoryStream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data is not compressed (Null compression codec).
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing the data.
                //Create a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using the given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of the AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using generic record to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




## <a name="sample-5-serialization-using-object-container-files-with-a-custom-compression-codec"></a>Ukázka 5: Serializace objektu kontejneru soubory pomocí vlastní kompresní kodek
Páté příklad ukazuje, jak používat vlastní kompresní kodek pro soubory kontejneru objektů Avro. Ukázka obsahující kód pro tento příklad si můžete stáhnout z [ukázky kódu Azure](http://code.msdn.microsoft.com/Serialize-data-with-the-67159111) lokality.

[Avro Specification](http://avro.apache.org/docs/current/spec.html#Required+Codecs) umožňuje využití používá volitelné kompresní kodek (kromě **Null** a **Deflate** výchozí nastavení). V tomto příkladu není implementace nové kodeků například Snappy (uvedené jako podporované volitelné kodeků v [Avro Specification](http://avro.apache.org/docs/current/spec.html#snappy)). Ukazuje, jak používat implementace rozhraní .NET Framework 4.5 [ **Deflate** ] [ deflate-110] kodeků, které poskytuje lepší algoritmus komprese na základě [zlib](http://zlib.net/) knihovnu komprese, než je výchozí verze rozhraní .NET Framework 4.

    //
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of the Deflate compression algorithm is used.
    // Ensure your C# project is set up accordingly.
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        #region Defining objects for serialization
        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET codec class contains two methods,
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed),
        //which are the key ones for data compression.
        //To enable a custom codec, one needs to implement these methods for the required codec.

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //cannot be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features.
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed).
        //Codec class uses classes for compressed and decompressed streams defined above.
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATIONS of Deflate, so CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified codec factory to be used in the reader.
        //It catches the attempt to use "Deflate" and provide  a custom codec.
        //For all other cases, it relies on the base class (CodecFactory).
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the custom compression codec (Deflate of .NET Framework 4.5).
            //
            //This sample uses memory stream for all operations related to serialization, deserialization and
            //object container manipulation, though file stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Here the custom codec is introduced. For convenience, the next commented code line shows how to use built-in Deflate.
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature, an AvroSerializerSettings instance is required
                    //when codec factory is explicitly specified.
                    //You may comment the line below if you want to use built-in Deflate (see next comment).
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    //Here the custom codec factory is introduced.
                    //For convenience, the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file using custom codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

## <a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>Ukázka 6: Pomocí Avro odesílat data do služby Microsoft Azure HDInsight
Šesté příklad ukazuje některé programátorských technik související s interakci s služby Azure HDInsight. Ukázka obsahující kód pro tento příklad si můžete stáhnout z [ukázky kódu Azure](https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3) lokality.

Ukázka provede následující úlohy:

* Připojí se k existujícímu clusteru služby HDInsight.
* Serializuje několik souborů CSV a odesílá výsledek do úložiště objektů Blob v Azure. (Soubory CSV jsou distribuovány spolu s vzorku a představují výpis z příloze Stock historická data distribuovat [Infochimps](http://www.infochimps.com/) dobu pod hodnotou 1970 2010. Ukázka čte data souboru CSV, převede záznamy na instance **Stock** třídy a pak je serializuje pomocí reflexe. Definice uložených typu je vytvořený z prostřednictvím nástroje Microsoft Avro Library kódu generování schématu JSON.
* Vytvoří novou tabulku externí názvem **akcií** v Hive a odkazy na jeho data nahráli v předchozím kroku.
* Provede dotaz pomocí Hive prostřednictvím **akcií** tabulky.

Kromě toho ukázku provádí postup pro čištění před a po provedení hlavních operací. Během vyčištění budou odebrána všechna související data objektů Blob v Azure a složky a je vyřadit tabulku Hive. Můžete také vyvolat postup vyčištění z příkazového řádku ukázka.

Ukázka má následující požadavky:

* Aktivní předplatné Microsoft Azure a jeho ID předplatného.
* Certifikát správy pro předplatné s odpovídající privátní klíč. Certifikát musí být nainstalován v aktuální uživatel privátní úložiště na počítači, který slouží ke spuštění ukázky.
* Aktivní clusteru HDInsight.
* Účet úložiště Azure odkazované ke clusteru HDInsight z předchozí součásti, společně s odpovídající primární nebo sekundární přístupový klíč.

Všechny informace z požadavků musí být zadán pro vzorový konfigurační soubor, před spuštěním ukázky. Existují dvě možné způsoby, jak to udělat:

* Upravte soubor app.config v kořenovém adresáři ukázka a potom sestavit ukázku
* Nejprve sestavit ukázku a pak upravte AvroHDISample.exe.config v adresáři sestavení

V obou případech by mělo být provedeno všechny úpravy **<appSettings>** v oddílu nastavení. Postupujte podle komentáře v souboru.
Ukázka spuštění z příkazového řádku tak, že spustíte následující příkaz (kde soubor .zip s ukázkou se předpokládá, že extrahovány do C:\AvroHDISample; Pokud, jinak použijte cestu k souboru relevantní):

    AvroHDISample run C:\AvroHDISample\Data

Vyčistěte clusteru, spusťte následující příkaz:

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx
