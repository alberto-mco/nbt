# nbt
A library to manage NBT files using Microsoft .Net Framework

## Introduction
The Named Binary Tag (also known as NBT) is a very simple file format that use binary tags to store data. This file format was created by Markus Persson for storing game data Minecraft.

Unfortunately, the NBT format is present in several compression formats:

- Uncompressed.
- Compressed with GZIP.
- Compressed with ZLIB.

## Standard tag types
| TagID | Name | Payload size (Bytes) | Description |
| --- | --- | --- | --- |
0 | TagEnd | 0 | The propouse of this tag is indicates the end of the opened TagCompound. |
1 | TagByte | 1 | A single unsigned byte. |
2 | TagShort | 2 | A single signed short. |
3 | TagInt | 4 | A single signed integer. |
4 | TagLong | 8 | A single signed long. |
5 | TagFloat | 4 | A single signed float. |
6 | TagDouble | 8 | A single signed double. |
7 | TagByteArray | Variable | Byte array. This tag is prefixed with a single signed integer that indicates the size of array. |
8 | TagString | Variable | A UTF-8 string. The string is prefixed with a single unsigned short that indicates the size of string. |
9 | TagList | Variable | List of nameless tags, all tags must be same tag type. The list is prefixed with the TagID of the items it contains (just one byte), and the length of the list with a single signed integer. This list is sortable. |
10 | TagCompound | Variable | List of named tags. This list is not sortable and his size is variable (without prefixed length) |
11 | TagIntArray | Variable | Integer Array. This tag is prefixed with a single integer that indicates the size of array. |

## My custom tag types
To give more options to NBT file format, i created new tag types.

| TagID | Name | Payload size (Bytes) | Description |
| --- | --- | --- | --- |
|252 | TagImage | Variable | To store an image. (System.Drawing.Image) |
|253 | TagIP | Variable | To store an IPAddress. |
|254 | TagMAC | Variable | To store a Physical Address. |
|251 | TagSByte | 1 | To store a signed Byte. |
|250 | TagUShort | 2 | To store an unsigned Short. |
|249 | TagUINT | 4 | To store an unsigned Integer. |
|248 | TagULong | 8 | To store an unsigned Long. |
|247 | TagShortArray | Variable | Short array. This tag is prefixed with a single integer that indicates the size of array. |
|246 | TagDateTime | 8 | To store a date time value. |
|245 | TagTimeSpan | 8 | To store a time span value. |
|244 | TagLongArray | Variable | Long array. This tag is prefixed with a single integer that indicates the size of array. |
|243 | TagFloatArray | Variable | Float array. This tag is prefixed with a single integer that indicates the size of array. |
|242 | TagDoubleArray | Variable | Double array. This tag is prefixed with a single integer that indicates the size of array. |
|241 | TagSByteArray | Variable | SByte array. This tag is prefixed with a single integer that indicates the size of array. |
|240 | TagUShortArray | Variable | UShort array. This tag is prefixed with a single integer that indicates the size of array. |
|239 | TagUIntArray | Variable | UInt array. This tag is prefixed with a single integer that indicates the size of array. |
|238 | TagULongArray | Variable | ULong array. This tag is prefixed with a single integer that indicates the size of array. |
|237 | TagImageArray | Variable | Image array. This tag is prefixed with a single integer that indicates the size of array. |

## File format rules
1. Everything is in big-endian.
2. All NBT files must begin with TagCompound.
3. All tags begin with a single byte that indicates his tag type.
4. All tags (except TagEnd and the items in TagList), begins with a TagString.
5. All tags of TagCompound must be closed by TagEnd.

## Format specifications and samples
The tags contains the following format:

|TagType (TagID) | TagString (Name) | Payload |
| --- | --- | --- |

The following sample show how this format store TagShort inside a TagCompound:
The following sample show how this format store TagShort inside a TagCompound

Theory:

``` C#
TagCompound: ('Test')
{
    TagShort: ('sample') : 123
}
```
Data on disk (hex format):

| (1) 10 | (2) 00 04 | (3) 54 65 73 74 | (4) 02 | (5) 00 06 | (6) 73 61 6D 70 7C 65 | (7) 00 7B | (8) 00 |
| --- | --- | --- | --- | --- | --- | --- | --- |

1. ID of TagCompound.
2. Length of the TagCompound name.
3. UTF-8 String ("Test").
4. Tag ID, in this case 2 because our tag is a TagShort.
5. Length of his name.
6. UTF-8 String ("sample").
7. Payload.
8. TagEnd (indicates the end of the TagCompound).

## Using the code
To use my library, it's necessary to import the following name space:
- NBT.IO (This name space contains everything to do with the file and its compression)
- NBT.Tags (Contains all supported tag types)

There are two namespaces (```NBT.Exceptions``` and ```NBT.Compression```).

```NBT.Exceptions``` contains all exception that can throw the library, and ```NBT.Compression``` contains all related with the compression.

Here is an example
```C#
using NBT.Tags;
using NBT.IO;

private void OpenFile(string filePath)
{
    //Create an instance of NBTFile to manage the file data.
    NBTFile nbtFile = new NBTFile();
    
    //Open the file using the function LoadFromFile, if you don't use a file, because you use a stream, you can also use the function LoadFromStream.
    nbtFile.Load(filePath);
    
    //Add a new string to the file
    nbtFile.RootTag.Add("Test string", new TagString("hello world!"));
    
    //Save the file
    nbtFile.Save(filePath);
}
```
