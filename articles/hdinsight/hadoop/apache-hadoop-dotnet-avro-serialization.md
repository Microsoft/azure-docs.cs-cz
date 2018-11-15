---
title: Serializace dat v Azure – Microsoft Avro Library - Apache Hadoop
description: Zjistěte, jak k serializaci a deserializaci data v Hadoop v HDInsight pomocí Microsoft Avro Library pro zachování paměti, databázi nebo souboru.
keywords: avro, hadoop avro
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.custom: hdiseo17may2017
ms.openlocfilehash: ae728cd1cfc27a17badcce319a8cd047b54ddb1e
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634001"
---
# <a name="serialize-data-in-apache-hadoop-with-the-microsoft-avro-library"></a>Serializace dat v systému Apache Hadoop pomocí Microsoft Avro Library

>[!NOTE]
>Avro SDK je již nejsou podporovány společností Microsoft. Knihovna je open source komunitou podporované. Zdroje pro knihovnu jsou umístěny v [Githubu](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

Toto téma ukazuje, jak používat [Microsoft Avro Library](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro) serializovat objekty a další datové struktury do datových proudů pro zachování paměti, databázi nebo souboru. Také ukazuje, jak deserializovala do požadovaného obnovit původní objekty.

[!INCLUDE [windows-only](../../../includes/hdinsight-windows-only.md)]

## <a name="apache-avro"></a>Apache Avro
<a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro Library</a> implementuje Apache Avro systém serializace dat pro prostředí Microsoft.NET. Apache Avro poskytuje formát pro výměnu kompaktní binární data pro serializaci. Používá <a href="http://www.json.org" target="_blank">JSON</a> pro definování jazykově nezávislého schématu, které poskytuje vzájemnou funkční spolupráci jazyka. Data sériovaná v jednom jazyce lze číst v jiném. Aktuálně jsou podporovány C, C++, C#, Java, PHP, Python a Ruby. Podrobné informace o formátu najdete v <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">Apache Avro Specification</a>. 

>[!NOTE]
>Microsoft Avro Library nepodporuje vzdálené procedury volání (RPC) součástí téhle specifikaci.
>

Serializovaná reprezentace objektu v systému Avro se skládá ze dvou částí: schéma a skutečnou hodnotu. Schématu Avro popisuje nezávislým na jazyku datový model serializovaných dat pomocí JSON. Zobrazí se vedle binární vyjádření data. Každý objekt má být zapsán s žádné režijní náklady na hodnotu, provádění rychlé serializace a reprezentaci malé s nezávisle na binární reprezentace schématu povoluje.

## <a name="the-hadoop-scenario"></a>Scénář Hadoop
Formát serializace Apache Avro se běžně používá v Azure HDInsight a dalších prostředí Apache Hadoop. Avro poskytuje pohodlný způsob, jak reprezentaci komplexní datové struktury v rámci úlohy Hadoop MapReduce. Formát souborů Avro (soubor Avro objektu kontejneru) byly navržené pro podporu Distribuovaný programovací model MapReduce. Klíčovou funkcí, které umožní, že distribuce je, že soubory jsou "rozdělitelné" v tom smyslu, že jeden můžete vyhledat libovolný bod v souboru a začít číst od určitého bloku.

## <a name="serialization-in-avro-library"></a>Serializace v knihovny Avro
Knihovna .NET pro Avro podporuje dva způsoby serializaci objektů:

* **reflexe** – je schéma JSON pro typy automaticky sestaveny z dat atributy smlouvy typů .NET k serializaci.
* **Obecný záznam** -schématu A JSON je jednoznačně uvedena v záznam reprezentovaný [ **AvroRecord** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) třídy při žádné typy .NET jsou k dispozici, aby popisují schéma pro data, která mají být serializován.

Pokud schéma dat známý zapisovače a čtečky datový proud, můžete data odesílají bez jeho schématu. V případech, pokud soubor Avro objektu kontejneru se použije, schéma je uloženo v souboru. Další parametry, jako je například kodek použitý pro kompresi dat, je možné zadat. Tyto scénáře jsou popsané v další podrobnosti a znázorněno v následujícím příkladu kódu:

## <a name="install-avro-library"></a>Instalace knihovny Avro
Vyžadují splnění následujících předpokladů před instalací knihovny:

* <a href="https://www.microsoft.com/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework 4</a>
* <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (6.0.4 nebo novější)

> [!Note]
> Microsoft Avro Library není nadále k dispozici jako balíček NuGet. Pokud chcete pomocí knihovny Avro klonování [úložiště Microsoft.Hadoop.Avro Github](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro) a kompilaci kódu na vašem počítači.

## <a name="compile-schemas-using-avro-library"></a>Kompilace schémat pomocí knihovny Avro
Microsoft Avro Library obsahuje nástroj generování kódu, který umožňuje vytváření typů jazyka C# automaticky v závislosti na předem definované schéma JSON. Nástroj pro generování kódu není distribuován jako binárního spustitelného souboru, ale je možné snadno sestavovat pomocí následujícího postupu:

1. Stáhněte si soubor .zip s nejnovější verzi sady SDK HDInsight zdrojový kód z <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">Microsoft .NET SDK pro Hadoop</a>. (Klikněte na tlačítko **Stáhnout** ikonu, ne **soubory ke stažení** kartu.)
2. Extrahujte sady HDInsight SDK do adresáře na počítači pomocí rozhraní .NET Framework 4 nainstalován a připojen k Internetu pro stahování potřebné závislosti balíčků NuGet. Níže předpokládáme, že zdrojový kód je extrahován do C:\SDK.
3. Přejděte do složky C:\SDK\src\Microsoft.Hadoop.Avro.Tools a spusťte build.bat. (Soubor volá MSBuild z distribuce 32bitová verze rozhraní .NET Framework. Pokud chcete použít 64bitovou verzi, upravte build.bat sledovat komentáře v souboru.) Ujistěte se, že je sestavení úspěšné. (U některých systémů, nástroj MSBuild může vytvořit upozornění. Tato upozornění nemají vliv na nástroji tak dlouho, dokud nejsou žádné chyby buildu.)
4. Nástroj pro kompilované je umístěn v C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools.

K seznámení s syntaxe příkazového řádku, spusťte následující příkaz ze složky, kde je umístěn nástroj pro generování kódu: `Microsoft.Hadoop.Avro.Tools help /c:codegen`

K otestování nástroj, můžete vygenerovat třídy jazyka C# z ukázkového souboru schématu JSON se zdrojovým kódem k dispozici. Spusťte následující příkaz:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

To by měl k vytvoření dvou C# soubory v aktuálním adresáři: SensorData.cs a Location.cs.

Informace o tom logiku, která používá nástroj pro generování kódu při převodu schématu JSON na typy jazyka C#, naleznete v souboru GenerationVerification.feature umístěn v C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc.

Obory názvů jsou extrahovány ze schématu JSON pomocí logiky popsané v souboru uvedené v předchozím odstavci. Obory názvů extrahovaná ze schématu přednost cokoli, co se poskytuje zároveň s parametrem /n nástroje příkazového řádku. Pokud chcete přepsat obory názvů obsažené v rámci schéma, použijte parametr /nf. Například pokud chcete změnit všechny obory názvů z SampleJSONSchema.avsc my.own.nspace, spusťte následující příkaz:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="about-the-samples"></a>O ukázky
Šest příkladů v tomto tématu ukazují různé scénáře, které podporuje Microsoft Avro Library. Microsoft Avro Library je navržen pro práci s datový proud. V těchto příkladech je zpracováván dat prostřednictvím datových proudů paměti namísto datové proudy souborů nebo databáze kvůli jednoduchosti a přehlednosti. Postup provést v produkčním prostředí závisí na požadavků na přesný scénář, zdroj dat a svazek, omezení výkonu a dalších faktorů.

První dva příklady ukazují, jak k serializaci a deserializaci data do vyrovnávací paměti datového proudu pomocí reflexe a obecné záznamy. Schéma v obou případech předpokládá, že je možné sdílet mezi čtečky a zapisovače out-of-band.

Třetí a čtvrtý příklady ukazují, jak k serializaci a deserializaci data pomocí souborů Avro objektu kontejneru. Pokud jsou data uložená v kontejneru soubor Avro, její schéma je vždy uložit s ním, protože schéma musí být sdílená pro deserializaci.

Ukázka obsahuje příklady první čtyři si můžete stáhnout z <a href="https://code.msdn.microsoft.com/Serialize-data-with-the-86055923" target="_blank">ukázky kódu Azure</a> lokality.

Pátý příklad ukazuje, jak používat vlastní kompresní kodek souborů Avro objektu kontejneru. Ukázka obsahující kód pro tento příklad je možné stáhnout z <a href="https://code.msdn.microsoft.com/Serialize-data-with-the-67159111" target="_blank">ukázky kódu Azure</a> lokality.

Šestý ukázka ukazuje způsob použití serializace Avro nahrání dat do úložiště objektů Blob v Azure a analyzovat je pomocí Hive pomocí clusteru služby HDInsight (Hadoop). Můžete ho stáhnout z <a href="https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3" target="_blank">ukázky kódu Azure</a> lokality.

Tady jsou odkazy na šest ukázky, které jsou popsány v tématu:

* <a href="#Scenario1">**Serializace pomocí reflexe** </a> – je schéma JSON pro typy, které mají být serializován automaticky sestaveny z dat atributy smlouvy.
* <a href="#Scenario2">**Serializace pomocí obecného záznam** </a> -schématu JSON je jednoznačně uvedena v záznamu, pokud žádný typ formátu .NET je k dispozici pro účely reflexe.
* <a href="#Scenario3">**Serializace pomocí souborů kontejneru objektů pomocí reflexe** </a> -schématu JSON je automaticky a nasdílel spolu s serializovaná data prostřednictvím soubor Avro objektu kontejneru.
* <a href="#Scenario4">**Serializace objektu kontejneru soubory pomocí obecného záznam** </a> -schématu JSON se explicitně zadat před serializace a sdílí spolu s daty prostřednictvím soubor Avro objektu kontejneru.
* <a href="#Scenario5">**Serializace objektu kontejneru soubory pomocí vlastní kompresní kodek** </a> – příklad ukazuje, jak vytvořit soubor Avro objektu kontejneru se na vlastní implementace .NET Deflate data kompresní kodek.
* <a href="#Scenario6">**Nahrání dat pro službu Microsoft Azure HDInsight pomocí Avro** </a> – příklad ukazuje, jak serializace Avro komunikuje se službou HDInsight. Aktivní předplatné Azure a přístup ke clusteru Azure HDInsight je potřeba spustit tento příklad.

## <a name="Scenario1"></a>Příklad 1: Serializace pomocí reflexe
Schéma JSON pro typy může automaticky sestavit Microsoft Avro Library prostřednictvím reflexe z dat kontraktu atributy objektů jazyka C# k serializaci. Vytvoří Microsoft Avro Library [ **IAvroSeralizer<T>**  ](https://msdn.microsoft.com/library/dn627341.aspx) k identifikaci polí, která mají být serializován.

V tomto příkladu objekty ( **SensorData** třídy se členem **umístění** struktury) serializují na datový proud paměti a tento datový proud je následně deserializovat. Výsledkem je pak porovnána s původní instance potvrdit, že **SensorData** je totožná s původní objekt obnovit.

Schéma v tomto příkladu se předpokládá, že sdílet mezi čtečky a zapisovače, aby nebylo třeba formát Avro objektu kontejneru. Příklad toho, jak k serializaci a deserializaci data do vyrovnávací paměti pomocí reflexe ve formátu kontejneru objektů při schématu musí být sdílená s daty, najdete v části <a href="#Scenario3">serializaci pomocí souborů kontejneru objektů pomocí reflexe</a>.

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


## <a name="sample-2-serialization-with-a-generic-record"></a>Příklad 2: Serializace pomocí obecného záznam
Při reflexi nelze použít, protože data nelze reprezentovat prostřednictvím třídy .NET s kontraktem dat, můžete v obecné záznamu explicitně zadán schématu JSON. Tato metoda je pomalejší než pomocí operace reflection. V takových případech schéma pro data mohou být také dynamické, tedy není známý v době kompilace. Data reprezentovaná jako souborů hodnot oddělených čárkami (CSV), jehož schéma neznámý, dokud se transformuje na formát Avro v době běhu je příkladem tento typ dynamického scénář.

Tento příklad ukazuje, jak vytvořit a používat [ **AvroRecord** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) s ohledem na schématu JSON, jak naplnit daty a jak k serializaci a deserializaci ji. Výsledkem je pak porovnána s původní instance, abyste se ujistili, že záznam obnovit totožná s původní.

Schéma v tomto příkladu se předpokládá, že sdílet mezi čtečky a zapisovače, aby nebylo třeba formát Avro objektu kontejneru. Příklad toho, jak k serializaci a deserializaci data do vyrovnávací paměti pomocí obecného záznamu ve formátu kontejneru objektů při schématu musí být součástí serializovaná data, najdete v článku <a href="#Scenario4">serializaci pomocí souborů kontejneru objektů s Obecný záznam</a> příklad.

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


## <a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>Ukázka 3: Serializace pomocí souborů kontejneru objektů a serializace pomocí reflexe
Tento příklad je podobný scénáři v <a href="#Scenario1"> první příklad</a>, ve kterém je implicitně zadané schéma pomocí reflexe. Rozdíl je, že tady, schéma se předpokládá, že známé čtečku, která ho deserializuje. **SensorData** k serializaci objektů a jejich implicitně zadané schéma jsou uloženy v souboru kontejneru objektu Avro reprezentována [ **AvroContainer** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) třídy.

Serializuje data v tomto příkladu se [ **SequentialWriter<SensorData>**  ](https://msdn.microsoft.com/library/dn627340.aspx) a deserializovat s [ **SequentialReader<SensorData>**  ](https://msdn.microsoft.com/library/dn627340.aspx). Výsledkem je pak porovnána s počáteční instance zajistit identity.

Data v souboru objektu kontejneru je komprimován pomocí výchozí [ **Deflate** ] [ deflate-100] kompresní kodek v rozhraní .NET Framework 4. Najdete v článku <a href="#Scenario5"> pátý příklad</a> v tomto tématu se naučíte poslední a získejte neuvěřitelnou verze operačního systému [ **Deflate** ] [ deflate-110] kompresní kodek k dispozici v rozhraní .NET Framework 4.5.

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


## <a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>Ukázka 4: Serializace pomocí obecného záznam souborů kontejneru objektů a serializace
Tento příklad je podobný scénáři v <a href="#Scenario2"> druhý příklad</a>, kde je schéma explicitně zadán s JSON. Rozdíl je, že tady, schéma se předpokládá, že známé čtečku, která ho deserializuje.

Datová sada testů se shromažďují do seznamu [ **AvroRecord** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) objekty prostřednictvím explicitně definované schéma JSON a pak uloženy v souboru kontejneru objekt reprezentovaný [  **AvroContainer** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) třídy. Tento soubor kontejneru vytvoří zapisovač, který se používá k serializaci dat, nekomprimovaný na datový proud paměti, která se pak uloží do souboru. [ **Codec.Null** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) parametr při vytváření čtecí funkce určuje, že není komprimována tato data.

Data se potom čtení ze souboru a deserializovat do kolekce objektů. Tato kolekce je ve srovnání s počáteční seznam záznamů Avro potvrďte, že jsou identické.

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
Pátý příklad ukazuje, jak používat vlastní kompresní kodek souborů Avro objektu kontejneru. Ukázka obsahující kód pro tento příklad je možné stáhnout z [ukázky kódu Azure](https://code.msdn.microsoft.com/Serialize-data-with-the-67159111) lokality.

[Avro Specification](http://avro.apache.org/docs/current/spec.html#Required+Codecs) umožňuje využití volitelné kompresní kodek (kromě **Null** a **Deflate** výchozí nastavení). V tomto příkladu není implementace nové kodeku, například Snappy (uvedené jako podporované volitelné kodek v [Avro Specification](http://avro.apache.org/docs/current/spec.html#snappy)). Ukazuje, jak použít implementaci rozhraní .NET Framework 4.5 [ **Deflate** ] [ deflate-110] kodek, který poskytuje lepší algoritmus komprese na základě [zlib ](http://zlib.net/) knihovna komprese než výchozí verze rozhraní .NET Framework 4.

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

## <a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>Ukázka 6: Použití Avro pro nahrání dat pro službu Microsoft Azure HDInsight
Šestý příklad ukazuje některé programovací techniky související s interakci se službou Azure HDInsight. Ukázka obsahující kód pro tento příklad je možné stáhnout z [ukázky kódu Azure](https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3) lokality.

Ukázka provede následující úlohy:

* Připojí se do existujícího clusteru služby HDInsight.
* Serializuje několika souborů CSV a nahraje výsledek do úložiště objektů Blob v Azure. (Soubory CSV se distribuují spolu s vzorku a představují extrakci z historických dat AMEX akcie distribuuje společnost [Infochimps](http://www.infochimps.com/) dobu 1970 2010. Ukázka načte data souborů CSV, převede záznamy k instancím typu **akcie** třídy a serializuje je pomocí reflexe. Definice základní typ je vytvořen z schématu JSON přes nástroj pro generování kódu Microsoft Avro Library.
* Vytvoří nový externí tabulky nazvané **akcie** v Hive a odkazy, nahrát ho k datům v předchozím kroku.
* Provede dotaz s použitím Hive nad **akcie** tabulky.

Kromě toho vzorku provádí vyčištění před a po provedení hlavních operací. Během vyčištění budou odebrána všechna související data objektů Blob v Azure a složek a tabulky Hive se zahodí. Můžete také vyvolat postup vyčištění z příkazového řádku vzorku.

Ukázka obsahuje následující požadavky:

* Aktivní předplatné Microsoft Azure a jeho ID předplatného.
* Certifikát pro správu předplatného pomocí odpovídajícího soukromého klíče. Certifikát by měl nainstalovaný v aktuální uživatel privátního úložiště na počítači používá ke spuštění ukázky.
* Aktivní clusteru HDInsight.
* Účet služby Azure Storage je přidružen ke clusteru HDInsight z předchozí požadavek, společně s odpovídající primární nebo sekundární přístupový klíč.

Všechny informace ze požadavky by měly být zadány do konfiguračního souboru vzorek, před spuštěním ukázky. Existují dva možné způsoby, jak to udělat:

* Upravte soubor app.config v kořenovém adresáři ukázkové a pak sestavit ukázku
* Nejdřív sestavit ukázku a pak upravte AvroHDISample.exe.config v adresáři sestavení

V obou případech by mělo být provedeno všechny úpravy **<appSettings>** části nastavení. Postupujte podle komentáře v souboru.
Spuštění ukázky z příkazového řádku spuštěním následujícího příkazu (kde soubor .zip s ukázkou se předpokládá, že extrahovat C:\AvroHDISample; Pokud v opačném případě použijte příslušné cestu k souboru):

    AvroHDISample run C:\AvroHDISample\Data

Vyčistit clusteru, spusťte následující příkaz:

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx
