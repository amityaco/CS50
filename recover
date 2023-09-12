#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[])
{
    if (argc != 2)
    {
        printf("Improper usage. use ./recover <filename>\n");
        return 1;
    }

    FILE *f = fopen(argv[1], "rb"); // Read info from the raw file
    // first byte 0xff 0xd8 0xff 0xef
    // last byte 0xe0 --- 0xef
    unsigned char buffer[512];
    if (f == NULL)
    {
        printf("Can't open file");
        return 1;
    }

    // while (!feof(f))   -->>  how i did it before this while loop
    int newfile_name = -1; // keep track of how many files found so far, start at -1
    FILE *img = NULL;      // initialize outside of loop, need to be global variable
    while (fread(buffer, sizeof(unsigned char), 512, f) == 512)
    {
        if ((buffer[0] == 0xff) && (buffer[1] == 0xd8) && (buffer[2] == 0xff) && ((buffer[3] & 0xf0) == 0xe0))
        {
            if (newfile_name != -1)
            {
                fclose(img);
            }
            char filename[8];
            newfile_name++;
            sprintf(filename, "%03i.jpg", newfile_name);
            img = fopen(filename, "wb");

            if (img == NULL)
            {
                printf("Could not open file\n");
                return 2;
            }
            fwrite(buffer, sizeof(unsigned char), 512, img); // write the first part of new jpg
        }
        else if (newfile_name != -1)
        {
            fwrite(buffer, sizeof(unsigned char), 512, img);  // continue writing other halves of existing jpg
        }
    }
    fclose(img);
    fclose(f);
}
