# C# Markov Chains (Generic)
Simple pattern 'learning' with Markov chains. After repeated 'repeatable' events, is able to generate more of those repeatable events that follow a similar frequency pattern. Useful for things like generating English-looking random text or making the best chess move after training across chess game histories.

The example below creates new text content based upon the word usage pattern of existing text content. For example, if you drop the works of Shakespeare into plain text files in a SamplesFolder then the resulting generated text will use the likelihood of any sequence of words appearing together to create new text that seems similar (it may pass computerised checks but will be obvious to a human reader).

Adjust the second parameter of the Train method call to change the quality of output.

Sample code to train based on all words in all files in a folder:

``` cs
DirectoryInfo Folder = new DirectoryInfo(SamplesFolder);
FileInfo[] Files = Folder.GetFiles("*.txt", SearchOption.AllDirectories);
if (Files.Length == 0) throw new Exception("No sample files found.");
Sample = new MarkovChains.Markov<string>(" ");
long LinesTrained = 0;
for (int FileNum = 0; FileNum < Files.Length; FileNum++)
{
    List<string> Content = new List<string>();
    using (StreamReader SampleFile = new StreamReader(Files[FileNum].FullName))
    {
        while (!(SampleFile.EndOfStream))
        {
            string Line = SampleFile.ReadLine().Trim();
            LinesTrained++;
            foreach (string Entry in Line.Split(new char[] { ' ' }, StringSplitOptions.RemoveEmptyEntries))
                Content.Add(Entry);
        }
        SampleFile.Close();
    }
    if (Content.Count > Chain) Sample.Train(Content, 4);
}
```

Sample code to generate new content based upon that trained usage pattern:

``` cs
List<string> Result = Sample.Generate(RequiredWordCount, true);
StringBuilder ResultString = new StringBuilder();
foreach (string Entry in Result) ResultString.Append(Entry + " ");
Console.Write(ResultString.ToString());
```
