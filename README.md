# color_scheme_creator

class ImageColorExtractor:
    A class for extracting the dominant colors from an image at a given URL.
    Attributes:
    -----------
    url : str
        The URL of the image to extract colors from.
    n : int
        The number of dominant colors to extract.

    Methods:
    --------
    display_colors(rgb_converted):
        Displays the dominant colors extracted from the image as a row of squares.
    rgb_color(color_array):
        Converts an array of colors in string format to RGB format.
    get_top_colors_raw():
        Extracts the dominant colors from the image at the specified URL and returns them as an array of RGB values.
        
    
    def __init__(self, url, n):
        """
        Initializes the `ImageColorExtractor` class with a URL and a number of dominant colors to extract.

        Parameters:
        -----------
        url : str
            The URL of the image to extract colors from.
        n : int
            The number of dominant colors to extract.
            
    def display_colors(self,rgb_converted):
        Displays the dominant colors extracted from the image as a row of squares.

        Parameters:
        -----------
        rgb_converted : list
            A list of RGB values for the dominant colors.
            
    def rgb_color(self, color_array):
        Converts an array of colors in string format to RGB format.

        Parameters:
        -----------
        color_array : list
            A list of colors in string format.

        Returns:
        --------
        output_colors : list
            A list of RGB values for the colors.

    def get_top_colors_raw(self):
        Extracts the dominant colors from the image at the specified URL and returns them as an array of RGB values.

        Returns:
        --------
        colors : ndarray
            An array of RGB values for the dominant colors.

<code>
import requests
from io import BytesIO
from PIL import Image
from sklearn.cluster import KMeans
import numpy as np
    
class ImageColorExtractor:
    
    def __init__(self, url, n):
        self.url = url
        self.n = n
        self.raw = self.get_top_colors_raw()
        self.rgb_converted = self.rgb_color(self.raw)

    def display_colors(self,rgb_converted):
        num_colors = len(rgb_converted)
        im = Image.new('RGB', (num_colors, 1), color=0)
        pixels = im.load()
        for i in range(num_colors):
            pixels[i, 0] = tuple(rgb_converted[i])
        im = im.resize((num_colors * 100, 100), resample=Image.NEAREST)
        im.show()

    def rgb_color(self, color_array):
        output_colors = []
        for colors1 in color_array:
            output_color = []
            for colors2 in colors1:
                output_color.append(int(colors2))
            output_colors.append(output_color)
        return output_colors

    def get_top_colors_raw(self):
        response = requests.get(self.url)
        image = Image.open(BytesIO(response.content))
        image = image.convert('RGB')
        image_array = np.array(image)
        image_array = image_array.reshape((image_array.shape[0] * image_array.shape[1]), 3)
        kmeans = KMeans(n_clusters=self.n)
        kmeans.fit(image_array)
        colors = kmeans.cluster_centers_
        distances = []
        for i in range(self.n):
            distance = 0
            for j in range(i):
                distance += np.linalg.norm(colors[i] - colors[j])
            distances.append(distance)
        sorted_indices = np.argsort(distances)[::-1]
        return colors[sorted_indices]
    
url = 'https://images.squarespace-cdn.com/content/v1/5b3c39fdf407b49ece5a22a9/1560245188210-R20OKTASBUNX2RMASSKU/The+Lyrebird.+95+x+100.jpg?format=1500w'
color_extractor = ImageColorExtractor(url, 5)
print(color_extractor.rgb_converted)
color_extractor.display_colors(color_extractor.rgb_converted)
</code>
