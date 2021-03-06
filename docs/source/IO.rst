Reading and Writing for Images and Transforms
*********************************************

.. _image-io:

Images
======

There are numerous file formats support by SimpleITK's image readers and writers.
Support for a particular format is handled by a specific ITK
`ImageIO <https://itk.org/Doxygen/html/classitk_1_1ImageIOBase.html>`_ class.
By default, the ImageIO is automatically determined for a particular file based
on the file name suffix and/or the contents of the file's header.
Advanced SimpleITK installations can configure or extend which file formats
are supported by SimpleITK. A list of registered ImageIO's can be found using the
``GetRegisteredImageIOs()`` method, but is posted here:

    - `BMPImageIO <https://itk.org/Doxygen/html/classitk_1_1BMPImageIO.html>`_ ( \*.bmp, \*.BMP )
    - `BioRadImageIO <https://itk.org/Doxygen/html/classitk_1_1BioRadImageIO.html>`_ ( \*.PIC, \*.pic )
    - `Bruker2dseqImageIO <https://itk.org/Doxygen/html/classitk_1_1Bruker2dseqImageIO.html>`_
    - `GDCMImageIO <https://itk.org/Doxygen/html/classitk_1_1GDCMImageIO.html>`_
    - `GE4ImageIO <https://itk.org/Doxygen/html/classitk_1_1GE4ImageIO.html>`_
    - `GE5ImageIO <https://itk.org/Doxygen/html/classitk_1_1GE5ImageIO.html>`_
    - `GiplImageIO <https://itk.org/Doxygen/html/classitk_1_1GiplImageIO.html>`_ ( \*.gipl \*.gipl.gz)
    - `HDF5ImageIO <https://itk.org/Doxygen/html/classitk_1_1HDF5ImageIO.html>`_
    - `JPEGImageIO <https://itk.org/Doxygen/html/classitk_1_1JPEGImageIO.html>`_ ( \*.jpg, \*.JPG, \*.jpeg, \*.JPEG )
    - `LSMImageIO <https://itk.org/Doxygen/html/classitk_1_1LSMImageIO.html>`_ ( \*.tif, \*.TIF, \*.tiff, \*.TIFF, \*.lsm, \*.LSM )
    - `MINCImageIO <https://itk.org/Doxygen/html/classitk_1_1MINCImageIO.html>`_ ( \*.mnc, \*.MNC )
    - `MRCImageIO <https://itk.org/Doxygen/html/classitk_1_1MRCImageIO.html>`_ ( \*.mrc, \*.rec )
    - `MetaImageIO <https://itk.org/Doxygen/html/classitk_1_1MetaImageIO.html>`_ ( \*.mha, \*.mhd )
    - `NiftiImageIO <https://itk.org/Doxygen/html/classitk_1_1NiftiImageIO.html>`_ ( \*.nia, \*.nii, \*.nii.gz, \*.hdr, \*.img, \*.img.gz )
    - `NrrdImageIO <https://itk.org/Doxygen/html/classitk_1_1NrrdImageIO.html>`_ ( \*.nrrd, \*.nhdr )
    - `PNGImageIO <https://itk.org/Doxygen/html/classitk_1_1PNGImageIO.html>`_ ( \*.png, \*.PNG )
    - `StimulateImageIO <https://itk.org/Doxygen/html/classitk_1_1StimulateImageIO.html>`_
    - `TIFFImageIO <https://itk.org/Doxygen/html/classitk_1_1TIFFImageIO.html>`_ ( \*.tif, \*.TIF, \*.tiff, \*.TIFF )
    - `VTKImageIO <https://itk.org/Doxygen/html/classitk_1_1VTKImageIO.html>`_ ( \*.vtk )


A read and write example using SimpleITK's ImageFileReader and ImageFileWriter classes:

.. code-block :: python

        import SimpleITK as sitk

        reader = sitk.ImageFileReader()
        reader.SetImageIO("BMPImageIO")
        reader.SetFileName(inputImageFileName)
        image = reader.Execute();

        writer = sitk.ImageFileWriter()
        writer.SetFileName(outputImageFileName)
        writer.Execute(image)

The above example specifies using the BMPImageIO to read the file.
If that line is omitted, SimpleITK would determine which IO to use automatically,
based on the file name's suffix and/or the file's header.

A more compact example using SimpleITK's procedural interface:

.. code-block :: python

        import SimpleITK as sitk

        image = sitk.ReadImage(inputImagefileName, imageIO="BMPImageIO")
        sitk.WriteImage(image, outputImagefileName)


.. _transformation-io:

Transformations
===============

In SimpleITK, transformation files can be written in several different formats.
Just like there are numerous IOs for images, there are several for transforms,
including TxtTransformIO, MINCTransformIO, HDF5TransformIO, and MatlabTransformIO
(although this list can be extended as well). These support a variety of file
formats, including .txt, .tfm, .xfm, .hdf and .mat.

Because of their size, displacement fields may require more careful attention.
To save a displacement field we recommend using one of the binary transformation
file formats (e.g. .hdf, .mat). Saving it in a text based format results in
significantly larger files and longer IO runtimes. Another option is to save
the displacement field found in a DisplacementFieldTransform object as an image
(.nrrd, .nhdr, .mha, .mhd, .nii, .nii.gz).

Take for example of a transformation written to and read from a file in Python:

.. code-block :: python

        basic_transform = sitk.Euler2DTransform()
        basic_transform.SetTranslation((2,3))

        sitk.WriteTransform(basic_transform, 'euler2D.tfm')
        read_result = sitk.ReadTransform('euler2D.tfm')

        assert(str(type(read_result) != type(basic_transform)))

``read_result`` will be an object of the generic ``sitk.Transform()`` class and ``basic_transform``
will be of ``sitk.Euler2DTransform()``, but both represent the same transformation. Although this
example only uses a single SimpleITK transformation, a .tfm file can hold a composite (set of
transformations).
