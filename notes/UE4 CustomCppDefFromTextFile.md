Build.cs

```cs
void AddMFDefinitions(ReadOnlyTargetRules Target)
	{
		string DefFilePath = ModuleDirectory.Substring(0, ModuleDirectory.IndexOf("Source")) + "MFDefinitions.txt";

		try
		{
			string[] Definitions = System.IO.File.ReadAllLines(DefFilePath, System.Text.Encoding.UTF8);

			foreach (string Def in Definitions)
			{
				string DefTrimmed = Def.Trim();
				if (!DefTrimmed.StartsWith("//"))
				{
					System.Console.WriteLine("Added MFDefinition: " + DefTrimmed);
					PublicDefinitions.Add(DefTrimmed);
				}
			}
		}
		catch (System.Exception e)
		{
			System.Console.WriteLine(e);
		}
	}
```


MFDefinitions.txt

```
// Uncomment to enable CPP Definitions
//MOREFUN_ENGINE_CUSTOM_UA
```
