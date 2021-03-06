---
title: "MSBuild Batching | Microsoft Docs"
ms.custom: ""
ms.date: "11/04/2016"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "vs-ide-sdk"
ms.tgt_pltfrm: ""
ms.topic: "article"
helpviewer_keywords: 
  - "batching [MSBuild]"
  - "MSBuild, batching"
ms.assetid: d35c085b-27b8-49d7-b6f8-8f2f3a0eec38
caps.latest.revision: 9
author: "kempb"
ms.author: "kempb"
manager: "ghogen"
translation.priority.ht: 
  - "cs-cz"
  - "de-de"
  - "es-es"
  - "fr-fr"
  - "it-it"
  - "ja-jp"
  - "ko-kr"
  - "pl-pl"
  - "pt-br"
  - "ru-ru"
  - "tr-tr"
  - "zh-cn"
  - "zh-tw"
---
# MSBuild Batching
[!INCLUDE[vstecmsbuild](../extensibility/internals/includes/vstecmsbuild_md.md)] has the ability to divide item lists into different categories, or batches, based on item metadata, and run a target or task one time with each batch.  
  
## Task Batching  
 Task batching allows you to simplify your project files by providing a way to divide item lists into different batches and pass each of those batches into a task separately. This means that a project file only needs to have the task and its attributes declared once, even though it can be run several times.  
  
 You specify that you want [!INCLUDE[vstecmsbuild](../extensibility/internals/includes/vstecmsbuild_md.md)] to perform batching with a task by using the %(*ItemMetaDataName*) notation in one of the task attributes. The following example splits the `Example` item list into batches based on the `Color` item metadata value, and passes each of the batches to the `MyTask` task separately.  
  
> [!NOTE]
>  If you do not reference the item list elsewhere in the task attributes, or the metadata name may be ambiguous, you can use the %(*ItemCollection.ItemMetaDataName*) notation to fully qualify the item metadata value to use for batching.  
  
```xml  
<Project  
    xmlns="http://schemas.microsoft.com/developer/msbuild/2003">  
  
    <ItemGroup>  
        <Example Include="Item1">  
            <Color>Blue</Color>  
        </Example>  
        <Example Include="Item2">  
            <Color>Red</Color>  
        </Example>  
    </ItemGroup>  
  
    <Target Name="RunMyTask">  
        <MyTask  
            Sources = "@(Example)"  
            Output = "%(Color)\MyFile.txt"/>  
    </Target>  
  
</Project>  
```  
  
 For more specific batching examples, see [Item Metadata in Task Batching](../msbuild/item-metadata-in-task-batching.md).  
  
## Target Batching  
 [!INCLUDE[vstecmsbuild](../extensibility/internals/includes/vstecmsbuild_md.md)] checks if the inputs and outputs of a target are up-to-date before it runs the target. If both inputs and outputs are up-to-date, the target is skipped. If a task inside of a target uses batching, [!INCLUDE[vstecmsbuild](../extensibility/internals/includes/vstecmsbuild_md.md)] needs to determine if the inputs and outputs for each batch of items is up-to-date. Otherwise, the target is executed every time it is hit.  
  
 The following example shows a `Target` element that contains an `Outputs` attribute with the %(*ItemMetaDataName*) notation. [!INCLUDE[vstecmsbuild](../extensibility/internals/includes/vstecmsbuild_md.md)] will divide the `Example` item list into batches based on the `Color` item metadata, and analyze the timestamps of the output files for each batch. If the outputs from a batch are not up-to-date, the target is run. Otherwise, the target is skipped.  
  
```xml  
<Project  
    xmlns="http://schemas.microsoft.com/developer/msbuild/2003">  
  
    <ItemGroup>  
        <Example Include="Item1">  
            <Color>Blue</Color>  
        </Example>  
        <Example Include="Item2">  
            <Color>Red</Color>  
        </Example>  
    </ItemGroup>  
  
    <Target Name="RunMyTask"  
        Inputs="@(Example)"  
        Outputs="%(Color)\MyFile.txt">  
        <MyTask  
            Sources = "@(Example)"  
            Output = "%(Color)\MyFile.txt"/>  
    </Target>  
  
</Project>  
```  
  
 For another example of target batching, see [Item Metadata in Target Batching](../msbuild/item-metadata-in-target-batching.md).  
  
## Property Functions Using Metadata  
 Batching can be controlled by property functions that include metadata. For example,  
  
 `$([System.IO.Path]::Combine($(RootPath),%(Compile.Identity)))`  
  
 uses <xref:System.IO.Path.Combine%2A> to combine a root folder path with a Compile item path.  
  
 Property functions may not appear within metadata values.  For example,  
  
 `%(Compile.FullPath.Substring(0,3))`  
  
 is not allowed.  
  
 For more information about property functions, see [Property Functions](../msbuild/property-functions.md).  
  
## See Also  
 [ItemMetadata Element (MSBuild)](../msbuild/itemmetadata-element-msbuild.md)   
 [MSBuild Concepts](../msbuild/msbuild-concepts.md)   
 [MSBuild Reference](../msbuild/msbuild-reference.md)   
 [Advanced Concepts](../msbuild/msbuild-advanced-concepts.md)