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
    if (Content.Count > 4) Sample.Train(Content, 4);
}
```

Sample code to generate new content based upon that trained usage pattern:

``` cs
long RequiredWordCount = 25000;
List<string> Result = Sample.Generate(RequiredWordCount, true);
StringBuilder ResultString = new StringBuilder();
foreach (string Entry in Result) ResultString.Append(Entry + " ");
Console.Write(ResultString.ToString());
```

The way it works is in essence quite simple:

* The first snippet reads in all paragraphs of text in all files in the sources folder.
* It then splits those into words and feeds the Markov instance those words in sequence. The result is that the instance 'learns' the probability of a particluar word being next when it has just seen a known sequence of the last 4 words (the parameter to the Train method).
* The second snippet uses those probabilities to 'guess' a stream of likely words based upon the existing patterns it has just learned.

The number 4 is not magic; it just works okay on sample English text and sort of represents the chunk of data considered at any one time. A higher number will produce more exact matches against the original and a lower number more randomness.

Another example might be to feed it complete sentences or paragraphs. In return the generator will give you the same complete sentences/paragraphs but in a pseudo-random sequence based upon the likelihood of one following another in the original inputs.
